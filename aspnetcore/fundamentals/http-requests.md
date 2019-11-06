---
title: ASP.NET Core에서 IHttpClientFactory를 사용하여 HTTP 요청 만들기
author: stevejgordon
description: IHttpClientFactory 인터페이스를 사용하여 ASP.NET Core에서 논리적 HttpClient 인스턴스를 관리하는 방법에 대해 알아봅니다.
ms.author: scaddie
ms.custom: mvc
ms.date: 10/27/2019
uid: fundamentals/http-requests
ms.openlocfilehash: a963833acfa12889c8ae3dac443962682e1cb931
ms.sourcegitcommit: 032113208bb55ecfb2faeb6d3e9ea44eea827950
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/31/2019
ms.locfileid: "73190590"
---
# <a name="make-http-requests-using-ihttpclientfactory-in-aspnet-core"></a><span data-ttu-id="ab97c-103">ASP.NET Core에서 IHttpClientFactory를 사용하여 HTTP 요청 만들기</span><span class="sxs-lookup"><span data-stu-id="ab97c-103">Make HTTP requests using IHttpClientFactory in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="ab97c-104">작성자: [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak),  [Steve Gordon](https://github.com/stevejgordon), [Rick Anderson](https://twitter.com/RickAndMSFT) 및 [Kirk Larkin](https://github.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="ab97c-104">By [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak),  [Steve Gordon](https://github.com/stevejgordon), [Rick Anderson](https://twitter.com/RickAndMSFT), and [Kirk Larkin](https://github.com/serpent5)</span></span>

<span data-ttu-id="ab97c-105">앱에서 <xref:System.Net.Http.IHttpClientFactory>를 등록하여 <xref:System.Net.Http.HttpClient> 인스턴스를 구성하고 만드는 데 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-105">An <xref:System.Net.Http.IHttpClientFactory> can be registered and used to configure and create <xref:System.Net.Http.HttpClient> instances in an app.</span></span> <span data-ttu-id="ab97c-106">`IHttpClientFactory`는 다음과 같은 이점을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-106">`IHttpClientFactory` offers the following benefits:</span></span>

* <span data-ttu-id="ab97c-107">논리적 `HttpClient` 인스턴스를 구성하고 이름을 지정하기 위한 중앙 위치를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-107">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="ab97c-108">예를 들어, *github*라는 클라이언트를 [GitHub](https://github.com/)에 액세스하도록 등록 및 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-108">For example, a client named  *github* could be registered and configured to access [GitHub](https://github.com/).</span></span> <span data-ttu-id="ab97c-109">일반적인 액세스를 위한 기본 클라이언트를 등록할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-109">A default client can be registered for general access.</span></span>
* <span data-ttu-id="ab97c-110">`HttpClient`에서 위임 처리기를 통해 나가는 미들웨어의 개념을 체계화합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-110">Codifies the concept of outgoing middleware via delegating handlers in `HttpClient`.</span></span> <span data-ttu-id="ab97c-111">Polly 기반 미들웨어에 대한 확장을 제공하여 `HttpClient`에서의 핸들러 위임을 활용합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-111">Provides extensions for Polly-based middleware to take advantage of delegating handlers in `HttpClient`.</span></span>
* <span data-ttu-id="ab97c-112">기본 `HttpClientMessageHandler` 인스턴스의 풀링 및 수명을 관리합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-112">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances.</span></span> <span data-ttu-id="ab97c-113">자동 관리가 `HttpClient` 수명을 수동으로 관리할 때 발생하는 일반적인 DNS(Domain Name System) 문제를 방지해 줍니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-113">Automatic management avoids common DNS (Domain Name System) problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="ab97c-114">팩터리에서 만든 클라이언트를 통해 전송된 모든 요청에 대해 구성 가능한 로깅 경험(`ILogger`을 통해)을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-114">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="ab97c-115">[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="ab97c-115">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="ab97c-116">이 항목 버전의 샘플 코드는 <xref:System.Text.Json>을 사용하여 HTTP 응답으로 반환된 JSON 콘텐츠를 역직렬화합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-116">The sample code in this topic version uses <xref:System.Text.Json> to deserialize JSON content returned in HTTP responses.</span></span> <span data-ttu-id="ab97c-117">`Json.NET` 및 `ReadAsAsync<T>`를 사용하는 샘플의 경우, 버전 선택기를 사용하여 이 항목의 2.x 버전을 선택하세요.</span><span class="sxs-lookup"><span data-stu-id="ab97c-117">For samples that use `Json.NET` and `ReadAsAsync<T>`, use the version selector to select a 2.x version of this topic.</span></span>

## <a name="consumption-patterns"></a><span data-ttu-id="ab97c-118">사용 패턴</span><span class="sxs-lookup"><span data-stu-id="ab97c-118">Consumption patterns</span></span>

<span data-ttu-id="ab97c-119">앱에서 `IHttpClientFactory`를 사용할 수 있는 몇 가지 방법이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-119">There are several ways `IHttpClientFactory` can be used in an app:</span></span>

* [<span data-ttu-id="ab97c-120">기본적인 사용 방법</span><span class="sxs-lookup"><span data-stu-id="ab97c-120">Basic usage</span></span>](#basic-usage)
* [<span data-ttu-id="ab97c-121">명명된 클라이언트</span><span class="sxs-lookup"><span data-stu-id="ab97c-121">Named clients</span></span>](#named-clients)
* [<span data-ttu-id="ab97c-122">형식화된 클라이언트</span><span class="sxs-lookup"><span data-stu-id="ab97c-122">Typed clients</span></span>](#typed-clients)
* [<span data-ttu-id="ab97c-123">생성된 클라이언트</span><span class="sxs-lookup"><span data-stu-id="ab97c-123">Generated clients</span></span>](#generated-clients)

<span data-ttu-id="ab97c-124">가장 좋은 방법은 앱의 요구 사항에 따라서 달라집니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-124">The best approach depends upon the app's requirements.</span></span>

### <a name="basic-usage"></a><span data-ttu-id="ab97c-125">기본적인 사용 방법</span><span class="sxs-lookup"><span data-stu-id="ab97c-125">Basic usage</span></span>

<span data-ttu-id="ab97c-126">`AddHttpClient`를 호출하여 `IHttpClientFactory`를 등록할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-126">`IHttpClientFactory` can be registered by calling `AddHttpClient`:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

<span data-ttu-id="ab97c-127">[종속성 주입(DI)](xref:fundamentals/dependency-injection)을 사용하여 `IHttpClientFactory`를 요청할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-127">An `IHttpClientFactory` can be requested using [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="ab97c-128">다음 코드는 `IHttpClientFactory`를 사용하여 `HttpClient` 인스턴스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-128">The following code uses `IHttpClientFactory` to create an `HttpClient` instance:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

<span data-ttu-id="ab97c-129">앞서 나온 예제에서와 같이 `IHttpClientFactory`를 사용하는 것은 기존 앱을 리팩터링하는 좋은 방법입니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-129">Using `IHttpClientFactory` like in the preceding example is a good way to refactor an existing app.</span></span> <span data-ttu-id="ab97c-130">`HttpClient`가 사용되는 방식에는 어떠한 영향도 없습니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-130">It has no impact on how `HttpClient` is used.</span></span> <span data-ttu-id="ab97c-131">기존 앱에서 `HttpClient` 인스턴스가 만들어지는 위치에서 해당 코드를 <xref:System.Net.Http.IHttpClientFactory.CreateClient*>에 대한 호출로 대체합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-131">In places where `HttpClient` instances are created in an existing app, replace those occurrences with calls to <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span></span>

### <a name="named-clients"></a><span data-ttu-id="ab97c-132">명명된 클라이언트</span><span class="sxs-lookup"><span data-stu-id="ab97c-132">Named clients</span></span>

<span data-ttu-id="ab97c-133">명명된 클라이언트는 다음과 같은 경우에 적합합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-133">Named clients are a good choice when:</span></span>

* <span data-ttu-id="ab97c-134">앱에서 `HttpClient`를 서로 다른 곳에서 여러 번 사용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-134">The app requires many distinct uses of `HttpClient`.</span></span>
* <span data-ttu-id="ab97c-135">여러 `HttpClient`가 서로 다른 구성을 갖습니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-135">Many `HttpClient`s have different configuration.</span></span>

<span data-ttu-id="ab97c-136">명명된 `HttpClient`에 대한 구성은 `Startup.ConfigureServices`에서 등록하는 동안 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-136">Configuration for a named `HttpClient` can be specified during registration in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

<span data-ttu-id="ab97c-137">위의 코드에서 클라이언트는 다음을 사용하여 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-137">In the preceding code the client is configured with:</span></span>

* <span data-ttu-id="ab97c-138">기본 주소 `https://api.github.com/`.</span><span class="sxs-lookup"><span data-stu-id="ab97c-138">The base address `https://api.github.com/`.</span></span>
* <span data-ttu-id="ab97c-139">GitHub API를 사용하는 데 필요한 헤더 2개.</span><span class="sxs-lookup"><span data-stu-id="ab97c-139">Two headers required to work with the GitHub API.</span></span>

#### <a name="createclient"></a><span data-ttu-id="ab97c-140">CreateClient</span><span class="sxs-lookup"><span data-stu-id="ab97c-140">CreateClient</span></span>

<span data-ttu-id="ab97c-141"><xref:System.Net.Http.IHttpClientFactory.CreateClient*>가 호출될 때마다</span><span class="sxs-lookup"><span data-stu-id="ab97c-141">Each time <xref:System.Net.Http.IHttpClientFactory.CreateClient*> is called:</span></span>

* <span data-ttu-id="ab97c-142">`HttpClient`의 새 인스턴스가 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-142">A new instance of `HttpClient` is created.</span></span>
* <span data-ttu-id="ab97c-143">구성 작업이 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-143">The configuration action is called.</span></span>

<span data-ttu-id="ab97c-144">명명된 클라이언트를 만들려면 `CreateClient`로 이름을 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-144">To create a named client, pass its name into `CreateClient`:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

<span data-ttu-id="ab97c-145">위의 코드에서는 요청이 호스트 이름을 지정할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-145">In the preceding code, the request doesn't need to specify a hostname.</span></span> <span data-ttu-id="ab97c-146">클라이언트에 대해 구성된 기본 주소가 사용되었므로 코드는 경로만 전달할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-146">The code can pass just the path, since the base address configured for the client is used.</span></span>

### <a name="typed-clients"></a><span data-ttu-id="ab97c-147">형식화된 클라이언트</span><span class="sxs-lookup"><span data-stu-id="ab97c-147">Typed clients</span></span>

<span data-ttu-id="ab97c-148">형식화된 클라이언트:</span><span class="sxs-lookup"><span data-stu-id="ab97c-148">Typed clients:</span></span>

* <span data-ttu-id="ab97c-149">문자열을 키로 사용할 필요가 없이 명명된 클라이언트와 동일한 기능을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-149">Provide the same capabilities as named clients without the need to use strings as keys.</span></span>
* <span data-ttu-id="ab97c-150">클라이언트를 사용할 때 IntelliSense 및 컴파일러 도움말을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-150">Provides IntelliSense and compiler help when consuming clients.</span></span>
* <span data-ttu-id="ab97c-151">특정 `HttpClient`을 구성하고 상호 작용하기 위해 단일 위치를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-151">Provide a single location to configure and interact with a particular `HttpClient`.</span></span> <span data-ttu-id="ab97c-152">예를 들어, 다음과 같은 경우에 단일 형식화된 클라이언트를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-152">For example, a single typed client might be used:</span></span>
  * <span data-ttu-id="ab97c-153">단일 백 엔드 엔드포인트에 대해.</span><span class="sxs-lookup"><span data-stu-id="ab97c-153">For a single backend endpoint.</span></span>
  * <span data-ttu-id="ab97c-154">엔드포인트를 처리하는 모든 로직을 캡슐화하기 위해.</span><span class="sxs-lookup"><span data-stu-id="ab97c-154">To encapsulate all logic dealing with the endpoint.</span></span>
* <span data-ttu-id="ab97c-155">DI로 작업하고 앱에서 필요할 경우 삽입할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-155">Work with DI and can be injected where required in the app.</span></span>

<span data-ttu-id="ab97c-156">형식화된 클라이언트는 생성자에서 `HttpClient` 매개 변수를 받습니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-156">A typed client accepts a `HttpClient` parameter in its constructor:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]

<span data-ttu-id="ab97c-157">위의 코드에서</span><span class="sxs-lookup"><span data-stu-id="ab97c-157">In the preceding code:</span></span>

* <span data-ttu-id="ab97c-158">구성이 형식화된 클라이언트로 이동되었습니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-158">The configuration is moved into the typed client.</span></span>
* <span data-ttu-id="ab97c-159">`HttpClient` 개체는 공용 속성으로 노출됩니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-159">The `HttpClient` object is exposed as a public property.</span></span>

<span data-ttu-id="ab97c-160">`HttpClient` 기능을 노출하는 API 특정 메서드를 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-160">API-specific methods can be created that expose `HttpClient` functionality.</span></span> <span data-ttu-id="ab97c-161">예를 들어, `GetAspNetDocsIssues` 메서드는 열린 문제를 검색하는 코드를 캡슐화합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-161">For example, the `GetAspNetDocsIssues` method encapsulates code to retrieve open issues.</span></span>

<span data-ttu-id="ab97c-162">다음 코드는 `Startup.ConfigureServices`에서 <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*>를 호출하여 형식화된 클라이언트 클래스를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-162">The following code calls <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> in `Startup.ConfigureServices` to register a typed client class:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

<span data-ttu-id="ab97c-163">형식화된 클라이언트는 DI를 사용하여 일시적으로 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-163">The typed client is registered as transient with DI.</span></span> <span data-ttu-id="ab97c-164">형식화된 클라이언트는 직접 주입되고 사용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-164">The typed client can be injected and consumed directly:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

<span data-ttu-id="ab97c-165">형식화된 클라이언트의 생성자 대신 `Startup.ConfigureServices`에서 등록하는 동안 형식화된 클라이언트에 대한 구성을 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-165">The configuration for a typed client can be specified during registration in `Startup.ConfigureServices`, rather than in the typed client's constructor:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

<span data-ttu-id="ab97c-166">형식화된 클라이언트 내에서 `HttpClient`를 캡슐화할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-166">The `HttpClient` can be encapsulated within a typed client.</span></span> <span data-ttu-id="ab97c-167">속성으로 노출하는 대신 내부적으로 `HttpClient` 인스턴스를 호출하는 메서드를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-167">Rather than exposing it as a property, define a method which calls the `HttpClient` instance internally:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

<span data-ttu-id="ab97c-168">위의 코드에서는 `HttpClient`가 private 필드로 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-168">In the preceding code, the `HttpClient` is stored in a private field.</span></span> <span data-ttu-id="ab97c-169">`HttpClient`에 대한 액세스는 public `GetRepos` 메서드에 의해 이루어집니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-169">Access to the `HttpClient` is by the public `GetRepos` method.</span></span>

### <a name="generated-clients"></a><span data-ttu-id="ab97c-170">생성된 클라이언트</span><span class="sxs-lookup"><span data-stu-id="ab97c-170">Generated clients</span></span>

<span data-ttu-id="ab97c-171">`IHttpClientFactory`는 [Refit](https://github.com/paulcbetts/refit)과 같은 타사 라이브러리와 함께 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-171">`IHttpClientFactory` can be used in combination with third-party libraries such as [Refit](https://github.com/paulcbetts/refit).</span></span> <span data-ttu-id="ab97c-172">Refit은 .NET용 REST 라이브러리입니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-172">Refit is a REST library for .NET.</span></span> <span data-ttu-id="ab97c-173">REST API를 라이브 인터페이스로 변환합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-173">It converts REST APIs into live interfaces.</span></span> <span data-ttu-id="ab97c-174">인터페이스의 구현은 `HttpClient`를 사용하여 외부 HTTP를 호출하도록 `RestService`에 의해 동적으로 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-174">An implementation of the interface is generated dynamically by the `RestService`, using `HttpClient` to make the external HTTP calls.</span></span>

<span data-ttu-id="ab97c-175">외부 API와 해당 응답을 나타내기 위한 인터페이스와 회신이 정의됩니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-175">An interface and a reply are defined to represent the external API and its response:</span></span>

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

<span data-ttu-id="ab97c-176">구현을 생성하기 위해 Refit를 사용하여 형식화된 클라이언트를 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-176">A typed client can be added, using Refit to generate the implementation:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddHttpClient("hello", c =>
    {
        c.BaseAddress = new Uri("http://localhost:5000");
    })
    .AddTypedClient(c => Refit.RestService.For<IHelloClient>(c));

    services.AddControllers();
}
```

<span data-ttu-id="ab97c-177">DI 및 Refit에서 제공한 구현을 통해 필요한 곳에서 정의된 인터페이스를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-177">The defined interface can be consumed where necessary, with the implementation provided by DI and Refit:</span></span>

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

## <a name="outgoing-request-middleware"></a><span data-ttu-id="ab97c-178">나가는 요청 미들웨어</span><span class="sxs-lookup"><span data-stu-id="ab97c-178">Outgoing request middleware</span></span>

<span data-ttu-id="ab97c-179">`HttpClient`에는 나가는 HTTP 요청을 위해 함께 연결될 수 있는 위임 처리기라는 개념이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-179">`HttpClient` has the concept of delegating handlers that can be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="ab97c-180">`IHttpClientFactory`:</span><span class="sxs-lookup"><span data-stu-id="ab97c-180">`IHttpClientFactory`:</span></span>

* <span data-ttu-id="ab97c-181">각 명명된 클라이언트에 적용할 처리기를 쉽게 정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-181">Simplifies defining the handlers to apply for each named client.</span></span>
* <span data-ttu-id="ab97c-182">나가는 요청 미들웨어 파이프라인을 만들기 위한 여러 처리기의 등록 및 연결을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-182">Supports registration and chaining of multiple handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="ab97c-183">이러한 처리기 각각은 나가는 요청 전후에 작업을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-183">Each of these handlers is able to perform work before and after the outgoing request.</span></span> <span data-ttu-id="ab97c-184">이 패턴은</span><span class="sxs-lookup"><span data-stu-id="ab97c-184">This pattern:</span></span>

  * <span data-ttu-id="ab97c-185">ASP.NET Core의 인바운드 미들웨어 파이프라인과 비슷합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-185">Is similar to the inbound middleware pipeline in ASP.NET Core.</span></span>
  * <span data-ttu-id="ab97c-186">다음과 같은 HTTP 요청을 둘러싼 횡단 관심사를 관리하기 위한 메커니즘을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-186">Provides a mechanism to manage cross-cutting concerns around HTTP requests, such as:</span></span>

    * <span data-ttu-id="ab97c-187">캐싱</span><span class="sxs-lookup"><span data-stu-id="ab97c-187">caching</span></span>
    * <span data-ttu-id="ab97c-188">오류 처리</span><span class="sxs-lookup"><span data-stu-id="ab97c-188">error handling</span></span>
    * <span data-ttu-id="ab97c-189">직렬화</span><span class="sxs-lookup"><span data-stu-id="ab97c-189">serialization</span></span>
    * <span data-ttu-id="ab97c-190">logging</span><span class="sxs-lookup"><span data-stu-id="ab97c-190">logging</span></span>

<span data-ttu-id="ab97c-191">위임 처리기를 만들려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-191">To create a delegating handler:</span></span>

* <span data-ttu-id="ab97c-192"><xref:System.Net.Http.DelegatingHandler>를 파생시킵니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-192">Derive from <xref:System.Net.Http.DelegatingHandler>.</span></span>
* <span data-ttu-id="ab97c-193"><xref:System.Net.Http.DelegatingHandler.SendAsync*>을 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-193">Override <xref:System.Net.Http.DelegatingHandler.SendAsync*>.</span></span> <span data-ttu-id="ab97c-194">파이프라인의 다음 처리기로 요청을 전달하기 전에 코드를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-194">Execute code before passing the request to the next handler in the pipeline:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

<span data-ttu-id="ab97c-195">위의 코드는 `X-API-KEY` 헤더가 요청에 있는지 여부를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-195">The preceding code checks if the `X-API-KEY` header is in the request.</span></span> <span data-ttu-id="ab97c-196">`X-API-KEY`가 누락된 경우 <xref:System.Net.HttpStatusCode.BadRequest>가 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-196">If `X-API-KEY` is missing, <xref:System.Net.HttpStatusCode.BadRequest> is returned.</span></span>

<span data-ttu-id="ab97c-197"><xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*?displayProperty=fullName>가 있는 `HttpClient`의 구성에는 둘 이상의 핸들러가 추가될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-197">More than one handler can be added to the configuration for a `HttpClient` with <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*?displayProperty=fullName>:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup2.cs?name=snippet1)]

<span data-ttu-id="ab97c-198">위의 코드에서 `ValidateHeaderHandler`는 DI에 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-198">In the preceding code, the `ValidateHeaderHandler` is registered with DI.</span></span> <span data-ttu-id="ab97c-199">`IHttpClientFactory`는 각 처리기에 대해 별도의 DI 범위를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-199">The `IHttpClientFactory` creates a separate DI scope for each handler.</span></span> <span data-ttu-id="ab97c-200">처리기는 모든 범위의 서비스에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-200">Handlers can depend upon services of any scope.</span></span> <span data-ttu-id="ab97c-201">처리기가 삭제되면 처리기가 사용하는 서비스가 삭제됩니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-201">Services that handlers depend upon are disposed when the handler is disposed.</span></span>

<span data-ttu-id="ab97c-202">일단 등록되면 처리기의 형식을 전달하여 <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*>를 호출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-202">Once registered, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> can be called, passing in the type for the handler.</span></span>

<span data-ttu-id="ab97c-203">여러 처리기를 실행해야 하는 순서에 따라 등록할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-203">Multiple handlers can be registered in the order that they should execute.</span></span> <span data-ttu-id="ab97c-204">각 처리기는 최종 `HttpClientHandler`가 요청을 실행할 때까지 다음 처리기를 래핑합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-204">Each handler wraps the next handler until the final `HttpClientHandler` executes the request:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

<span data-ttu-id="ab97c-205">다음 방법 중 하나를 사용하여 메시지 처리기와 요청별 상태를 공유하세요.</span><span class="sxs-lookup"><span data-stu-id="ab97c-205">Use one of the following approaches to share per-request state with message handlers:</span></span>

* <span data-ttu-id="ab97c-206">[HttpRequestMessage.Properties](xref:System.Net.Http.HttpRequestMessage.Properties)를 사용하여 데이터를 처리기로 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-206">Pass data into the handler using [HttpRequestMessage.Properties](xref:System.Net.Http.HttpRequestMessage.Properties).</span></span>
* <span data-ttu-id="ab97c-207"><xref:Microsoft.AspNetCore.Http.IHttpContextAccessor>를 사용하여 현재 요청에 액세스합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-207">Use <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> to access the current request.</span></span>
* <span data-ttu-id="ab97c-208">사용자 지정 <xref:System.Threading.AsyncLocal`1> 스토리지 개체를 만들어 데이터를 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-208">Create a custom <xref:System.Threading.AsyncLocal`1> storage object to pass the data.</span></span>

## <a name="use-polly-based-handlers"></a><span data-ttu-id="ab97c-209">Polly 기반 처리기 사용</span><span class="sxs-lookup"><span data-stu-id="ab97c-209">Use Polly-based handlers</span></span>

<span data-ttu-id="ab97c-210">`IHttpClientFactory`는 타사 라이브러리 [Polly](https://github.com/App-vNext/Polly)와 통합됩니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-210">`IHttpClientFactory` integrates with the third-party library [Polly](https://github.com/App-vNext/Polly).</span></span> <span data-ttu-id="ab97c-211">Polly는 .NET용 포괄적인 회복탄력성 및 일시적 오류 처리 라이브러리입니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-211">Polly is a comprehensive resilience and transient fault-handling library for .NET.</span></span> <span data-ttu-id="ab97c-212">개발자는 이를 사용하여 재시도, 회로 차단기, 시간 초과, 격벽 격리 및 대체(Fallback) 같은 정책을 유연하고 스레드로부터 안전한 방식으로 표현할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-212">It allows developers to express policies such as Retry, Circuit Breaker, Timeout, Bulkhead Isolation, and Fallback in a fluent and thread-safe manner.</span></span>

<span data-ttu-id="ab97c-213">구성된 `HttpClient` 인스턴스를 통해 Polly 정책을 사용할 수 있도록 확장 메서드가 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-213">Extension methods are provided to enable the use of Polly policies with configured `HttpClient` instances.</span></span> <span data-ttu-id="ab97c-214">Polly 확장은 클라이언트에 Polly 기반 처리기를 추가하는 것을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-214">The Polly extensions support adding Polly-based handlers to clients.</span></span> <span data-ttu-id="ab97c-215">Polly를 사용하려면 [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet 패키지가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-215">Polly requires the [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet package.</span></span>

### <a name="handle-transient-faults"></a><span data-ttu-id="ab97c-216">일시적인 오류 처리</span><span class="sxs-lookup"><span data-stu-id="ab97c-216">Handle transient faults</span></span>

<span data-ttu-id="ab97c-217">오류는 외부 HTTP 호출이 일시적인 경우 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-217">Faults typically occur when external HTTP calls are transient.</span></span> <span data-ttu-id="ab97c-218"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*>를 사용하면 정책에서 일시적인 오류를 처리하도록 정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-218"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> allows a policy to be defined to handle transient errors.</span></span> <span data-ttu-id="ab97c-219">`AddTransientHttpErrorPolicy`를 사용하여 구성한 정책은 다음과 같은 응답을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-219">Policies configured with `AddTransientHttpErrorPolicy` handle the following responses:</span></span>

* <xref:System.Net.Http.HttpRequestException>
* <span data-ttu-id="ab97c-220">HTTP 5xx</span><span class="sxs-lookup"><span data-stu-id="ab97c-220">HTTP 5xx</span></span>
* <span data-ttu-id="ab97c-221">HTTP 408</span><span class="sxs-lookup"><span data-stu-id="ab97c-221">HTTP 408</span></span>

<span data-ttu-id="ab97c-222">`AddTransientHttpErrorPolicy`는 가능한 일시적 오류를 나타내는 오류를 처리하기 위해 구성된 `PolicyBuilder` 개체에 대한 액세스를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-222">`AddTransientHttpErrorPolicy` provides access to a `PolicyBuilder` object configured to handle errors representing a possible transient fault:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup3.cs?name=snippet1)]

<span data-ttu-id="ab97c-223">위의 코드에서는 `WaitAndRetryAsync` 정책을 정의하고 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-223">In the preceding code, a `WaitAndRetryAsync` policy is defined.</span></span> <span data-ttu-id="ab97c-224">실패한 요청은 최대 세 번까지 다시 시도되며 시도 간 600밀리초의 지연 간격을 둡니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-224">Failed requests are retried up to three times with a delay of 600 ms between attempts.</span></span>

### <a name="dynamically-select-policies"></a><span data-ttu-id="ab97c-225">동적으로 정책 선택</span><span class="sxs-lookup"><span data-stu-id="ab97c-225">Dynamically select policies</span></span>

<span data-ttu-id="ab97c-226">Polly 기반 처리기를 추가하는 데 사용할 수 있는 확장 메서드(예: <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandler*>)가 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-226">Extension methods are provided to add Polly-based handlers, for example, <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandler*>.</span></span> <span data-ttu-id="ab97c-227">다음 `AddPolicyHandler` 오버로드는 요청을 검사하여 어느 정책을 적용할지 결정합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-227">The following `AddPolicyHandler` overload inspects the request to decide which policy to apply:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

<span data-ttu-id="ab97c-228">위의 코드에서는 나가는 요청이 HTTP GET인 경우 10초 시간 제한이 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-228">In the preceding code, if the outgoing request is an HTTP GET, a 10-second timeout is applied.</span></span> <span data-ttu-id="ab97c-229">다른 HTTP 메서드의 경우 30초 시간 제한이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-229">For any other HTTP method, a 30-second timeout is used.</span></span>

### <a name="add-multiple-polly-handlers"></a><span data-ttu-id="ab97c-230">여러 Polly 처리기 추가</span><span class="sxs-lookup"><span data-stu-id="ab97c-230">Add multiple Polly handlers</span></span>

<span data-ttu-id="ab97c-231">Polly 정책을 중첩하는 것은 일반적입니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-231">It's common to nest Polly policies:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

<span data-ttu-id="ab97c-232">앞의 예제에서:</span><span class="sxs-lookup"><span data-stu-id="ab97c-232">In the preceding example:</span></span>

* <span data-ttu-id="ab97c-233">두 개의 처리기가 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-233">Two handlers are added.</span></span>
* <span data-ttu-id="ab97c-234">첫 번째 처리기는 재시도 정책을 추가하기 위해 <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*>를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-234">The first handler uses <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> to add a retry policy.</span></span> <span data-ttu-id="ab97c-235">실패한 요청은 최대 세 번까지 다시 시도됩니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-235">Failed requests are retried up to three times.</span></span>
* <span data-ttu-id="ab97c-236">두 번째 호출 `AddTransientHttpErrorPolicy`는 회로 차단기 정책을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-236">The second `AddTransientHttpErrorPolicy` call adds a circuit breaker policy.</span></span> <span data-ttu-id="ab97c-237">5번의 시도가 순차적으로 실패하는 경우 추가적인 외부 요청은 30초 동안 차단됩니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-237">Further external requests are blocked for 30 seconds if 5 failed attempts occur sequentially.</span></span> <span data-ttu-id="ab97c-238">회로 차단기 정책은 상태를 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-238">Circuit breaker policies are stateful.</span></span> <span data-ttu-id="ab97c-239">이 클라이언트를 통한 모든 호출은 동일한 회로 상태를 공유합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-239">All calls through this client share the same circuit state.</span></span>

### <a name="add-policies-from-the-polly-registry"></a><span data-ttu-id="ab97c-240">Polly 레지스트리로부터 정책 추가</span><span class="sxs-lookup"><span data-stu-id="ab97c-240">Add policies from the Polly registry</span></span>

<span data-ttu-id="ab97c-241">정기적으로 사용되는 정책을 관리하는 방법은 정책을 한 번 정의하고 이를 `PolicyRegistry`에 등록하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-241">An approach to managing regularly used policies is to define them once and register them with a `PolicyRegistry`.</span></span>

<span data-ttu-id="ab97c-242">다음 코드에서:</span><span class="sxs-lookup"><span data-stu-id="ab97c-242">In the following code:</span></span>

* <span data-ttu-id="ab97c-243">“regular” 정책과 “long” 정책이 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-243">The "regular" and "long" polices are added.</span></span>
* <span data-ttu-id="ab97c-244"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandlerFromRegistry*>가 레지스트리로부터 “regular” 정책과 “long” 정책을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-244"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandlerFromRegistry*>  adds the "regular" and "long" policies from the registry.</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup4.cs?name=snippet1)]

<span data-ttu-id="ab97c-245">`IHttpClientFactory` 및 Polly 통합에 대한 자세한 내용은 [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="ab97c-245">For more information on `IHttpClientFactory` and Polly integrations, see the [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span></span>

## <a name="httpclient-and-lifetime-management"></a><span data-ttu-id="ab97c-246">HttpClient 및 수명 관리</span><span class="sxs-lookup"><span data-stu-id="ab97c-246">HttpClient and lifetime management</span></span>

<span data-ttu-id="ab97c-247">`IHttpClientFactory`에서 `CreateClient`가 호출될 때마다 새로운 `HttpClient` 인스턴스가 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-247">A new `HttpClient` instance is returned each time `CreateClient` is called on the `IHttpClientFactory`.</span></span> <span data-ttu-id="ab97c-248">명명된 클라이언트마다 <xref:System.Net.Http.HttpMessageHandler>가 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-248">An <xref:System.Net.Http.HttpMessageHandler> is created per named client.</span></span> <span data-ttu-id="ab97c-249">팩터리는 `HttpMessageHandler` 인스턴스의 수명을 관리합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-249">The factory manages the lifetimes of the `HttpMessageHandler` instances.</span></span>

<span data-ttu-id="ab97c-250">`IHttpClientFactory`는 리소스 사용을 줄이기 위해 팩터리에서 만든 `HttpMessageHandler` 인스턴스를 풀링합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-250">`IHttpClientFactory` pools the `HttpMessageHandler` instances created by the factory to reduce resource consumption.</span></span> <span data-ttu-id="ab97c-251">수명이 만료되지 않은 경우, 새 `HttpClient` 인스턴스를 만들 때 풀에서 `HttpMessageHandler` 인스턴스가 재사용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-251">An `HttpMessageHandler` instance may be reused from the pool when creating a new `HttpClient` instance if its lifetime hasn't expired.</span></span>

<span data-ttu-id="ab97c-252">일반적으로 각 처리기는 자체적인 기본 HTTP 연결을 관리하므로 처리기의 풀링이 적합합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-252">Pooling of handlers is desirable as each handler typically manages its own underlying HTTP connections.</span></span> <span data-ttu-id="ab97c-253">필요한 것보다 많은 처리기를 만들면 연결 지연이 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-253">Creating more handlers than necessary can result in connection delays.</span></span> <span data-ttu-id="ab97c-254">또한 일부 처리기는 무한정으로 연결을 열어 놓아 처리기가 DNS(Domain Name System) 변경에 대응하는 것을 막을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-254">Some handlers also keep connections open indefinitely, which can prevent the handler from reacting to DNS (Domain Name System) changes.</span></span>

<span data-ttu-id="ab97c-255">기본 처리기 수명은 2분입니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-255">The default handler lifetime is two minutes.</span></span> <span data-ttu-id="ab97c-256">명명된 클라이언트별 기준으로 기본값을 재정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-256">The default value can be overridden on a per named client basis:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup5.cs?name=snippet1)]

<span data-ttu-id="ab97c-257">`HttpClient` 인스턴스는 일반적으로 삭제가 필요하지 **않은** .NET 개체로 간주할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-257">`HttpClient` instances can generally be treated as .NET objects **not** requiring disposal.</span></span> <span data-ttu-id="ab97c-258">삭제는 나가는 요청을 취소하고 <xref:System.IDisposable.Dispose*>를 호출한 후에는 지정된 `HttpClient` 인스턴스가 사용될 수 없도록 보장합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-258">Disposal cancels outgoing requests and guarantees the given `HttpClient` instance can't be used after calling <xref:System.IDisposable.Dispose*>.</span></span> <span data-ttu-id="ab97c-259">`IHttpClientFactory`는 `HttpClient` 인스턴스에서 사용되는 리소스를 추적하고 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-259">`IHttpClientFactory` tracks and disposes resources used by `HttpClient` instances.</span></span>

<span data-ttu-id="ab97c-260">장기간 단일 `HttpClient` 인스턴스를 활성 상태로 유지하는 것은 `IHttpClientFactory`가 등장하기 전에 사용되던 일반적인 패턴입니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-260">Keeping a single `HttpClient` instance alive for a long duration is a common pattern used before the inception of `IHttpClientFactory`.</span></span> <span data-ttu-id="ab97c-261">이 패턴은 `IHttpClientFactory`로 마이그레이션한 이후에는 불필요합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-261">This pattern becomes unnecessary after migrating to `IHttpClientFactory`.</span></span>

## <a name="logging"></a><span data-ttu-id="ab97c-262">로깅</span><span class="sxs-lookup"><span data-stu-id="ab97c-262">Logging</span></span>

<span data-ttu-id="ab97c-263">`IHttpClientFactory`을 통해 만든 클라이언트는 모든 요청에 대한 로그 메시지를 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-263">Clients created via `IHttpClientFactory` record log messages for all requests.</span></span> <span data-ttu-id="ab97c-264">기본 로그 메시지를 보려면 로깅 구성에서 적절한 정보 수준을 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-264">Enable the appropriate information level in the logging configuration to see the default log messages.</span></span> <span data-ttu-id="ab97c-265">요청 헤더의 로깅 등과 같은 추가 로깅은 추적 수준에서만 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-265">Additional logging, such as the logging of request headers, is only included at trace level.</span></span>

<span data-ttu-id="ab97c-266">각 클라이언트에 사용되는 로그 범주는 클라이언트의 이름을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-266">The log category used for each client includes the name of the client.</span></span> <span data-ttu-id="ab97c-267">예를 들어, *MyNamedClient*라는 클라이언트는 “System.Net.Http.HttpClient.**MyNamedClient**.LogicalHandler”의 범주를 사용하여 메시지를 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-267">A client named *MyNamedClient*, for example, logs messages with a category of "System.Net.Http.HttpClient.**MyNamedClient**.LogicalHandler".</span></span> <span data-ttu-id="ab97c-268">*LogicalHandler*라는 접미사가 붙은 메시지는 요청 처리기 파이프라인 외부에서 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-268">Messages suffixed with *LogicalHandler* occur outside the request handler pipeline.</span></span> <span data-ttu-id="ab97c-269">요청 시 파이프라인의 다른 모든 처리기에서 이를 처리하기 전에 메시지가 기록됩니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-269">On the request, messages are logged before any other handlers in the pipeline have processed it.</span></span> <span data-ttu-id="ab97c-270">응답 시 다른 모든 파이프라인 처리기가 응답을 받은 후에 메시지가 기록됩니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-270">On the response, messages are logged after any other pipeline handlers have received the response.</span></span>

<span data-ttu-id="ab97c-271">로깅은 요청 처리기 파이프라인 내부에서도 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-271">Logging also occurs inside the request handler pipeline.</span></span> <span data-ttu-id="ab97c-272">*MyNamedClient* 예제에서 해당 메시지는 로그 범주 “System.Net.Http.HttpClient.**MyNamedClient**.ClientHandler”에 대해 기록됩니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-272">In the *MyNamedClient* example, those messages are logged with the log category "System.Net.Http.HttpClient.**MyNamedClient**.ClientHandler".</span></span> <span data-ttu-id="ab97c-273">요청의 경우 이는 요청이 전송되기 직전 및 다른 모든 처리기가 실행된 후에 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-273">For the request, this occurs after all other handlers have run and immediately before the request is sent.</span></span> <span data-ttu-id="ab97c-274">응답 시 이 로깅은 처리기 파이프라인을 통해 응답이 다시 전달되기 전의 응답 상태를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-274">On the response, this logging includes the state of the response before it passes back through the handler pipeline.</span></span>

<span data-ttu-id="ab97c-275">파이프라인 외부 및 내부에서 로깅을 사용하도록 설정하면 다른 파이프라인 처리기가 수행한 변경 내용을 검사할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-275">Enabling logging outside and inside the pipeline enables inspection of the changes made by the other pipeline handlers.</span></span> <span data-ttu-id="ab97c-276">여기에는 요청 헤더 또는 응답 상태 코드에 대한 변경이 포함될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-276">This may include changes to request headers or to the response status code.</span></span>

<span data-ttu-id="ab97c-277">로그 범주에 클라이언트의 이름을 포함하면 명명된 특정 클라이언트에 대한 로그 필터링을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-277">Including the name of the client in the log category enables log filtering for specific named clients.</span></span>

## <a name="configure-the-httpmessagehandler"></a><span data-ttu-id="ab97c-278">HttpMessageHandler 구성</span><span class="sxs-lookup"><span data-stu-id="ab97c-278">Configure the HttpMessageHandler</span></span>

<span data-ttu-id="ab97c-279">클라이언트가 사용하는 내부 `HttpMessageHandler`의 구성을 제어해야 할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-279">It may be necessary to control the configuration of the inner `HttpMessageHandler` used by a client.</span></span>

<span data-ttu-id="ab97c-280">`IHttpClientBuilder`는 명명된 또는 형식화된 클라이언트를 추가할 때 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-280">An `IHttpClientBuilder` is returned when adding named or typed clients.</span></span> <span data-ttu-id="ab97c-281"><xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> 확장 메서드는 대리자를 정의하는 데 사용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-281">The <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> extension method can be used to define a delegate.</span></span> <span data-ttu-id="ab97c-282">대리자는 해당 클라이언트가 사용하는 기본 `HttpMessageHandler`을 만들고 구성하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-282">The delegate is used to create and configure the primary `HttpMessageHandler` used by that client:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup6.cs?name=snippet1)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a><span data-ttu-id="ab97c-283">콘솔 앱에서 IHttpClientFactory 사용</span><span class="sxs-lookup"><span data-stu-id="ab97c-283">Use IHttpClientFactory in a console app</span></span>

<span data-ttu-id="ab97c-284">콘솔 앱에서 프로젝트에 다음 패키지 참조를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-284">In a console app, add the following package references to the project:</span></span>

* [<span data-ttu-id="ab97c-285">Microsoft.Extensions.Hosting</span><span class="sxs-lookup"><span data-stu-id="ab97c-285">Microsoft.Extensions.Hosting</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [<span data-ttu-id="ab97c-286">Microsoft.Extensions.Http</span><span class="sxs-lookup"><span data-stu-id="ab97c-286">Microsoft.Extensions.Http</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Http)

<span data-ttu-id="ab97c-287">다음 예제에서는</span><span class="sxs-lookup"><span data-stu-id="ab97c-287">In the following example:</span></span>

* <span data-ttu-id="ab97c-288"><xref:System.Net.Http.IHttpClientFactory>는 [제너릭 호스트의](xref:fundamentals/host/generic-host) 서비스 컨테이너에 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-288"><xref:System.Net.Http.IHttpClientFactory> is registered in the [Generic Host's](xref:fundamentals/host/generic-host) service container.</span></span>
* <span data-ttu-id="ab97c-289">`MyService`에서는 `HttpClient`를 만드는 데 사용하는 서비스에서 클라이언트 팩터리 인스턴스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-289">`MyService` creates a client factory instance from the service, which is used to create an `HttpClient`.</span></span> <span data-ttu-id="ab97c-290">`HttpClient`는 웹 페이지를 검색하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-290">`HttpClient` is used to retrieve a webpage.</span></span>
* <span data-ttu-id="ab97c-291">`Main`을 통해서는 서비스의 `GetPage` 메서드를 실행하고 웹 페이지 콘텐츠의 처음 500자를 콘솔에 씁니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-291">`Main` creates a scope to execute the service's `GetPage` method and write the first 500 characters of the webpage content to the console.</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="additional-resources"></a><span data-ttu-id="ab97c-292">추가 자료</span><span class="sxs-lookup"><span data-stu-id="ab97c-292">Additional resources</span></span>

* [<span data-ttu-id="ab97c-293">HttpClientFactory를 사용하여 복원력 있는 HTTP 요청 구현</span><span class="sxs-lookup"><span data-stu-id="ab97c-293">Use HttpClientFactory to implement resilient HTTP requests</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [<span data-ttu-id="ab97c-294">HttpClientFactory 및 Polly 정책을 통해 지수 백오프를 사용하여 HTTP 호출 다시 시도 구현</span><span class="sxs-lookup"><span data-stu-id="ab97c-294">Implement HTTP call retries with exponential backoff with HttpClientFactory and Polly policies</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [<span data-ttu-id="ab97c-295">회로 차단기 패턴 구현</span><span class="sxs-lookup"><span data-stu-id="ab97c-295">Implement the Circuit Breaker pattern</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="ab97c-296">작성자: [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak) 및 [Steve Gordon](https://github.com/stevejgordon)</span><span class="sxs-lookup"><span data-stu-id="ab97c-296">By [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak), and [Steve Gordon](https://github.com/stevejgordon)</span></span>

<span data-ttu-id="ab97c-297">앱에서 <xref:System.Net.Http.IHttpClientFactory>를 등록하여 <xref:System.Net.Http.HttpClient> 인스턴스를 구성하고 만드는 데 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-297">An <xref:System.Net.Http.IHttpClientFactory> can be registered and used to configure and create <xref:System.Net.Http.HttpClient> instances in an app.</span></span> <span data-ttu-id="ab97c-298">이는 다음과 같은 이점을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-298">It offers the following benefits:</span></span>

* <span data-ttu-id="ab97c-299">논리적 `HttpClient` 인스턴스를 구성하고 이름을 지정하기 위한 중앙 위치를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-299">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="ab97c-300">예를 들어, [GitHub](https://github.com/)에 액세스하는 *github* 클라이언트를 등록 및 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-300">For example, a *github* client can be registered and configured to access [GitHub](https://github.com/).</span></span> <span data-ttu-id="ab97c-301">기본 클라이언트는 다른 용도로 등록할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-301">A default client can be registered for other purposes.</span></span>
* <span data-ttu-id="ab97c-302">`HttpClient`에서 위임 처리기를 통해 나가는 미들웨어의 개념을 체계화하고 Polly 기반 미들웨어에 대한 확장을 제공하여 이를 활용합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-302">Codifies the concept of outgoing middleware via delegating handlers in `HttpClient` and provides extensions for Polly-based middleware to take advantage of that.</span></span>
* <span data-ttu-id="ab97c-303">`HttpClient` 수명을 수동으로 관리할 때 발생하는 일반적인 DNS 문제를 피하기 위해 기본 `HttpClientMessageHandler` 인스턴스의 풀링 및 수명을 관리합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-303">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances to avoid common DNS problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="ab97c-304">팩터리에서 만든 클라이언트를 통해 전송된 모든 요청에 대해 구성 가능한 로깅 경험(`ILogger`을 통해)을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-304">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="ab97c-305">[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="ab97c-305">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="consumption-patterns"></a><span data-ttu-id="ab97c-306">사용 패턴</span><span class="sxs-lookup"><span data-stu-id="ab97c-306">Consumption patterns</span></span>

<span data-ttu-id="ab97c-307">앱에서 `IHttpClientFactory`를 사용할 수 있는 몇 가지 방법이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-307">There are several ways `IHttpClientFactory` can be used in an app:</span></span>

* [<span data-ttu-id="ab97c-308">기본적인 사용 방법</span><span class="sxs-lookup"><span data-stu-id="ab97c-308">Basic usage</span></span>](#basic-usage)
* [<span data-ttu-id="ab97c-309">명명된 클라이언트</span><span class="sxs-lookup"><span data-stu-id="ab97c-309">Named clients</span></span>](#named-clients)
* [<span data-ttu-id="ab97c-310">형식화된 클라이언트</span><span class="sxs-lookup"><span data-stu-id="ab97c-310">Typed clients</span></span>](#typed-clients)
* [<span data-ttu-id="ab97c-311">생성된 클라이언트</span><span class="sxs-lookup"><span data-stu-id="ab97c-311">Generated clients</span></span>](#generated-clients)

<span data-ttu-id="ab97c-312">어떤 방법도 다른 방법에 비해 절대적으로 우수하지는 않습니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-312">None of them are strictly superior to another.</span></span> <span data-ttu-id="ab97c-313">가장 좋은 방법은 앱의 제약 조건에 따라서 달라집니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-313">The best approach depends upon the app's constraints.</span></span>

### <a name="basic-usage"></a><span data-ttu-id="ab97c-314">기본적인 사용 방법</span><span class="sxs-lookup"><span data-stu-id="ab97c-314">Basic usage</span></span>

<span data-ttu-id="ab97c-315">`IHttpClientFactory`는 `Startup.ConfigureServices` 메서드 내에서 `IServiceCollection`의 `AddHttpClient` 확장 메서드를 호출하여 등록할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-315">The `IHttpClientFactory` can be registered by calling the `AddHttpClient` extension method on the `IServiceCollection`, inside the `Startup.ConfigureServices` method.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

<span data-ttu-id="ab97c-316">일단 등록하고 나면 코드는 [DI(종속성 주입)](xref:fundamentals/dependency-injection)를 사용하여 서비스를 주입할 수 있는 모든 곳에서 `IHttpClientFactory`를 받을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-316">Once registered, code can accept an `IHttpClientFactory` anywhere services can be injected with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="ab97c-317">`IHttpClientFactory`는 `HttpClient` 인스턴스를 만드는 데 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-317">The `IHttpClientFactory` can be used to create a `HttpClient` instance:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

<span data-ttu-id="ab97c-318">이러한 방식으로 `IHttpClientFactory`를 사용하는 것은 기존 앱을 리팩터링할 수 있는 좋은 방법입니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-318">Using `IHttpClientFactory` in this fashion is a good way to refactor an existing app.</span></span> <span data-ttu-id="ab97c-319">`HttpClient`가 사용되는 방식에는 어떠한 영향도 없습니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-319">It has no impact on the way `HttpClient` is used.</span></span> <span data-ttu-id="ab97c-320">`HttpClient` 인스턴스가 현재 만들어지는 위치에서 해당 코드를 <xref:System.Net.Http.IHttpClientFactory.CreateClient*>에 대한 호출로 대체합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-320">In places where `HttpClient` instances are currently created, replace those occurrences with a call to <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span></span>

### <a name="named-clients"></a><span data-ttu-id="ab97c-321">명명된 클라이언트</span><span class="sxs-lookup"><span data-stu-id="ab97c-321">Named clients</span></span>

<span data-ttu-id="ab97c-322">앱이 각각 다른 구성을 사용하는 많은 개별적인 `HttpClient`의 사용을 필요로 하는 경우 선택할 수 있는 방법은 **명명된 클라이언트**를 사용하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-322">If an app requires many distinct uses of `HttpClient`, each with a different configuration, an option is to use **named clients**.</span></span> <span data-ttu-id="ab97c-323">명명된 `HttpClient`에 대한 구성은 `Startup.ConfigureServices`에서 등록하는 동안 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-323">Configuration for a named `HttpClient` can be specified during registration in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

<span data-ttu-id="ab97c-324">위의 코드에서는 `AddHttpClient`를 호출하여 *github*이라는 이름을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-324">In the preceding code, `AddHttpClient` is called, providing the name *github*.</span></span> <span data-ttu-id="ab97c-325">이 클라이언트에는 일부 기본 구성&mdash;즉 GitHub API를 작동하는 데 필요한 기준 주소 및 두 개의 헤더가 적용되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-325">This client has some default configuration applied&mdash;namely the base address and two headers required to work with the GitHub API.</span></span>

<span data-ttu-id="ab97c-326">`CreateClient`를 호출할 때마다 `HttpClient`의 새 인스턴스를 만들고 구성 작업을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-326">Each time `CreateClient` is called, a new instance of `HttpClient` is created and the configuration action is called.</span></span>

<span data-ttu-id="ab97c-327">명명된 클라이언트를 사용하기 위해서 `CreateClient`에 문자열 매개 변수를 전달할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-327">To consume a named client, a string parameter can be passed to `CreateClient`.</span></span> <span data-ttu-id="ab97c-328">만들 클라이언트의 이름을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-328">Specify the name of the client to be created:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

<span data-ttu-id="ab97c-329">위의 코드에서는 요청이 호스트 이름을 지정할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-329">In the preceding code, the request doesn't need to specify a hostname.</span></span> <span data-ttu-id="ab97c-330">클라이언트에 대해 구성된 기본 주소를 사용하므로 경로만 전달할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-330">It can pass just the path, since the base address configured for the client is used.</span></span>

### <a name="typed-clients"></a><span data-ttu-id="ab97c-331">형식화된 클라이언트</span><span class="sxs-lookup"><span data-stu-id="ab97c-331">Typed clients</span></span>

<span data-ttu-id="ab97c-332">형식화된 클라이언트:</span><span class="sxs-lookup"><span data-stu-id="ab97c-332">Typed clients:</span></span>

* <span data-ttu-id="ab97c-333">문자열을 키로 사용할 필요가 없이 명명된 클라이언트와 동일한 기능을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-333">Provide the same capabilities as named clients without the need to use strings as keys.</span></span>
* <span data-ttu-id="ab97c-334">클라이언트를 사용할 때 IntelliSense 및 컴파일러 도움말을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-334">Provides IntelliSense and compiler help when consuming clients.</span></span>
* <span data-ttu-id="ab97c-335">특정 `HttpClient`을 구성하고 상호 작용하기 위해 단일 위치를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-335">Provide a single location to configure and interact with a particular `HttpClient`.</span></span> <span data-ttu-id="ab97c-336">예를 들어 단일 형식화된 클라이언트는 단일 백 엔드 엔드포인트에 사용될 수 있으며 해당 엔드포인트를 다루는 모든 논리를 캡슐화할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-336">For example, a single typed client might be used for a single backend endpoint and encapsulate all logic dealing with that endpoint.</span></span>
* <span data-ttu-id="ab97c-337">DI로 작업하고 앱에서 필요할 경우 삽입할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-337">Work with DI and can be injected where required in your app.</span></span>

<span data-ttu-id="ab97c-338">형식화된 클라이언트는 생성자에서 `HttpClient` 매개 변수를 받습니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-338">A typed client accepts a `HttpClient` parameter in its constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]

<span data-ttu-id="ab97c-339">위의 코드에서는 구성이 형식화된 클라이언트로 이동되었습니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-339">In the preceding code, the configuration is moved into the typed client.</span></span> <span data-ttu-id="ab97c-340">`HttpClient` 개체는 공용 속성으로 노출됩니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-340">The `HttpClient` object is exposed as a public property.</span></span> <span data-ttu-id="ab97c-341">`HttpClient` 기능을 노출하는 API 특정 메서드를 정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-341">It's possible to define API-specific methods that expose `HttpClient` functionality.</span></span> <span data-ttu-id="ab97c-342">`GetAspNetDocsIssues` 메서드는 GitHub 리포지토리에서 공개된 최신 문제를 구문 분석하고 쿼리하는 데 필요한 코드를 캡슐화합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-342">The `GetAspNetDocsIssues` method encapsulates the code needed to query for and parse out the latest open issues from a GitHub repository.</span></span>

<span data-ttu-id="ab97c-343">형식화된 클라이언트를 등록하기 위해서 `Startup.ConfigureServices`에서 형식화된 클라이언트 클래스를 지정하여 제네릭 <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> 확장 메서드를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-343">To register a typed client, the generic <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> extension method can be used within `Startup.ConfigureServices`, specifying the typed client class:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

<span data-ttu-id="ab97c-344">형식화된 클라이언트는 DI를 사용하여 일시적으로 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-344">The typed client is registered as transient with DI.</span></span> <span data-ttu-id="ab97c-345">형식화된 클라이언트는 직접 주입되고 사용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-345">The typed client can be injected and consumed directly:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

<span data-ttu-id="ab97c-346">원한다면 형식화된 클라이언트의 생성자 대신 `Startup.ConfigureServices`에서 등록하는 동안 형식화된 클라이언트에 대한 구성을 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-346">If preferred, the configuration for a typed client can be specified during registration in `Startup.ConfigureServices`, rather than in the typed client's constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

<span data-ttu-id="ab97c-347">형식화된 클라이언트 내에서 `HttpClient`를 완전히 캡슐화할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-347">It's possible to entirely encapsulate the `HttpClient` within a typed client.</span></span> <span data-ttu-id="ab97c-348">속성으로 노출하는 대신 공용 메서드를 제공하여 내부적으로 `HttpClient` 인스턴스를 호출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-348">Rather than exposing it as a property, public methods can be provided which call the `HttpClient` instance internally.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

<span data-ttu-id="ab97c-349">위의 코드에서는 `HttpClient`가 전용 필드로 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-349">In the preceding code, the `HttpClient` is stored as a private field.</span></span> <span data-ttu-id="ab97c-350">외부 호출을 하기 위한 모든 액세스는 `GetRepos` 메서드를 거칩니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-350">All access to make external calls goes through the `GetRepos` method.</span></span>

### <a name="generated-clients"></a><span data-ttu-id="ab97c-351">생성된 클라이언트</span><span class="sxs-lookup"><span data-stu-id="ab97c-351">Generated clients</span></span>

<span data-ttu-id="ab97c-352">`IHttpClientFactory`는 [Refit](https://github.com/paulcbetts/refit)과 같은 다른 타사 라이브러리와 함께 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-352">`IHttpClientFactory` can be used in combination with other third-party libraries such as [Refit](https://github.com/paulcbetts/refit).</span></span> <span data-ttu-id="ab97c-353">Refit은 .NET용 REST 라이브러리입니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-353">Refit is a REST library for .NET.</span></span> <span data-ttu-id="ab97c-354">REST API를 라이브 인터페이스로 변환합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-354">It converts REST APIs into live interfaces.</span></span> <span data-ttu-id="ab97c-355">인터페이스의 구현은 `HttpClient`를 사용하여 외부 HTTP를 호출하도록 `RestService`에 의해 동적으로 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-355">An implementation of the interface is generated dynamically by the `RestService`, using `HttpClient` to make the external HTTP calls.</span></span>

<span data-ttu-id="ab97c-356">외부 API와 해당 응답을 나타내기 위한 인터페이스와 회신이 정의됩니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-356">An interface and a reply are defined to represent the external API and its response:</span></span>

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

<span data-ttu-id="ab97c-357">구현을 생성하기 위해 Refit를 사용하여 형식화된 클라이언트를 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-357">A typed client can be added, using Refit to generate the implementation:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddHttpClient("hello", c =>
    {
        c.BaseAddress = new Uri("https://localhost:5001");
    })
    .AddTypedClient(c => Refit.RestService.For<IHelloClient>(c));

    services.AddMvc();
}
```

<span data-ttu-id="ab97c-358">DI 및 Refit에서 제공한 구현을 통해 필요한 곳에서 정의된 인터페이스를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-358">The defined interface can be consumed where necessary, with the implementation provided by DI and Refit:</span></span>

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

## <a name="outgoing-request-middleware"></a><span data-ttu-id="ab97c-359">나가는 요청 미들웨어</span><span class="sxs-lookup"><span data-stu-id="ab97c-359">Outgoing request middleware</span></span>

<span data-ttu-id="ab97c-360">`HttpClient`에는 나가는 HTTP 요청을 위해 함께 연결될 수 있는 위임 처리기라는 개념이 이미 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-360">`HttpClient` already has the concept of delegating handlers that can be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="ab97c-361">`IHttpClientFactory`를 사용하면 각 명명된 클라이언트에 적용할 처리기를 쉽게 정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-361">The `IHttpClientFactory` makes it easy to define the handlers to apply for each named client.</span></span> <span data-ttu-id="ab97c-362">나가는 요청 미들웨어 파이프라인을 만들기 위한 여러 처리기의 등록 및 연결을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-362">It supports registration and chaining of multiple handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="ab97c-363">이러한 처리기 각각은 나가는 요청 전후에 작업을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-363">Each of these handlers is able to perform work before and after the outgoing request.</span></span> <span data-ttu-id="ab97c-364">이 패턴은 ASP.NET Core의 인바운드 미들웨어 파이프라인과 비슷합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-364">This pattern is similar to the inbound middleware pipeline in ASP.NET Core.</span></span> <span data-ttu-id="ab97c-365">이 패턴은 캐싱, 오류 처리, 직렬화 및 로깅을 포함한 HTTP 요청을 둘러싼 횡단 관심사를 관리하기 위한 메커니즘을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-365">The pattern provides a mechanism to manage cross-cutting concerns around HTTP requests, including caching, error handling, serialization, and logging.</span></span>

<span data-ttu-id="ab97c-366">처리기를 만들려면 <xref:System.Net.Http.DelegatingHandler>에서 파생되는 클래스를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-366">To create a handler, define a class deriving from <xref:System.Net.Http.DelegatingHandler>.</span></span> <span data-ttu-id="ab97c-367">파이프라인의 다음 처리기로 요청을 전달하기 전에 코드를 실행하려면 `SendAsync` 메서드를 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-367">Override the `SendAsync` method to execute code before passing the request to the next handler in the pipeline:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

<span data-ttu-id="ab97c-368">위의 코드에서는 기본 처리기를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-368">The preceding code defines a basic handler.</span></span> <span data-ttu-id="ab97c-369">`X-API-KEY` 헤더가 요청에 포함되었는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-369">It checks to see if an `X-API-KEY` header has been included on the request.</span></span> <span data-ttu-id="ab97c-370">헤더가 누락된 경우 HTTP 호출을 방지하고 적합한 응답을 반환할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-370">If the header is missing, it can avoid the HTTP call and return a suitable response.</span></span>

<span data-ttu-id="ab97c-371">등록하는 동안 하나 이상의 처리기가 `HttpClient`의 구성에 추가될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-371">During registration, one or more handlers can be added to the configuration for a `HttpClient`.</span></span> <span data-ttu-id="ab97c-372">이 작업은 <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>의 확장 메서드를 통해 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-372">This task is accomplished via extension methods on the <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet5)]

<span data-ttu-id="ab97c-373">위의 코드에서 `ValidateHeaderHandler`는 DI에 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-373">In the preceding code, the `ValidateHeaderHandler` is registered with DI.</span></span> <span data-ttu-id="ab97c-374">`IHttpClientFactory`는 각 처리기에 대해 별도의 DI 범위를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-374">The `IHttpClientFactory` creates a separate DI scope for each handler.</span></span> <span data-ttu-id="ab97c-375">처리기는 모든 범위의 서비스에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-375">Handlers are free to depend upon services of any scope.</span></span> <span data-ttu-id="ab97c-376">처리기가 삭제되면 처리기가 사용하는 서비스가 삭제됩니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-376">Services that handlers depend upon are disposed when the handler is disposed.</span></span>

<span data-ttu-id="ab97c-377">일단 등록되면 처리기의 형식을 전달하여 <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*>를 호출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-377">Once registered, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> can be called, passing in the type for the handler.</span></span>

<span data-ttu-id="ab97c-378">여러 처리기를 실행해야 하는 순서에 따라 등록할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-378">Multiple handlers can be registered in the order that they should execute.</span></span> <span data-ttu-id="ab97c-379">각 처리기는 최종 `HttpClientHandler`가 요청을 실행할 때까지 다음 처리기를 래핑합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-379">Each handler wraps the next handler until the final `HttpClientHandler` executes the request:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

<span data-ttu-id="ab97c-380">다음 방법 중 하나를 사용하여 메시지 처리기와 요청별 상태를 공유하세요.</span><span class="sxs-lookup"><span data-stu-id="ab97c-380">Use one of the following approaches to share per-request state with message handlers:</span></span>

* <span data-ttu-id="ab97c-381">`HttpRequestMessage.Properties`를 사용하여 데이터를 처리기로 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-381">Pass data into the handler using `HttpRequestMessage.Properties`.</span></span>
* <span data-ttu-id="ab97c-382">`IHttpContextAccessor`를 사용하여 현재 요청에 액세스합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-382">Use `IHttpContextAccessor` to access the current request.</span></span>
* <span data-ttu-id="ab97c-383">사용자 지정 `AsyncLocal` 스토리지 개체를 만들어 데이터를 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-383">Create a custom `AsyncLocal` storage object to pass the data.</span></span>

## <a name="use-polly-based-handlers"></a><span data-ttu-id="ab97c-384">Polly 기반 처리기 사용</span><span class="sxs-lookup"><span data-stu-id="ab97c-384">Use Polly-based handlers</span></span>

<span data-ttu-id="ab97c-385">`IHttpClientFactory`는 [Polly](https://github.com/App-vNext/Polly)라는 유명한 타사 라이브러리와 통합됩니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-385">`IHttpClientFactory` integrates with a popular third-party library called [Polly](https://github.com/App-vNext/Polly).</span></span> <span data-ttu-id="ab97c-386">Polly는 .NET용 포괄적인 회복탄력성 및 일시적 오류 처리 라이브러리입니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-386">Polly is a comprehensive resilience and transient fault-handling library for .NET.</span></span> <span data-ttu-id="ab97c-387">개발자는 이를 사용하여 재시도, 회로 차단기, 시간 초과, 격벽 격리 및 대체(Fallback) 같은 정책을 유연하고 스레드로부터 안전한 방식으로 표현할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-387">It allows developers to express policies such as Retry, Circuit Breaker, Timeout, Bulkhead Isolation, and Fallback in a fluent and thread-safe manner.</span></span>

<span data-ttu-id="ab97c-388">구성된 `HttpClient` 인스턴스를 통해 Polly 정책을 사용할 수 있도록 확장 메서드가 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-388">Extension methods are provided to enable the use of Polly policies with configured `HttpClient` instances.</span></span> <span data-ttu-id="ab97c-389">Polly 확장은:</span><span class="sxs-lookup"><span data-stu-id="ab97c-389">The Polly extensions:</span></span>

* <span data-ttu-id="ab97c-390">클라이언트에 Polly 기반 처리기 추가를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-390">Support adding Polly-based handlers to clients.</span></span>
* <span data-ttu-id="ab97c-391">[Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet 패키지를 설치한 후 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-391">Can be used after installing the [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet package.</span></span> <span data-ttu-id="ab97c-392">이 패키지는 ASP.NET Core 공유 프레임워크에 포함되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-392">The package isn't included in the ASP.NET Core shared framework.</span></span>

### <a name="handle-transient-faults"></a><span data-ttu-id="ab97c-393">일시적인 오류 처리</span><span class="sxs-lookup"><span data-stu-id="ab97c-393">Handle transient faults</span></span>

<span data-ttu-id="ab97c-394">가장 일반적인 오류는 외부 HTTP 호출이 일시적인 경우 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-394">Most common faults occur when external HTTP calls are transient.</span></span> <span data-ttu-id="ab97c-395">일시적인 오류를 처리하기 위한 정책을 정의할 수 있는 `AddTransientHttpErrorPolicy`라는 편리한 확장 메서드가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-395">A convenient extension method called `AddTransientHttpErrorPolicy` is included which allows a policy to be defined to handle transient errors.</span></span> <span data-ttu-id="ab97c-396">이 확장 메서드로 구성된 정책은 `HttpRequestException`, HTTP 5xx 응답 및 HTTP 408 응답을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-396">Policies configured with this extension method handle `HttpRequestException`, HTTP 5xx responses, and HTTP 408 responses.</span></span>

<span data-ttu-id="ab97c-397">`AddTransientHttpErrorPolicy` 확장은 `Startup.ConfigureServices` 내에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-397">The `AddTransientHttpErrorPolicy` extension can be used within `Startup.ConfigureServices`.</span></span> <span data-ttu-id="ab97c-398">이 확장은 가능한 일시적 오류를 나타내는 오류를 처리하기 위해 구성된 `PolicyBuilder` 개체에 대한 액세스를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-398">The extension provides access to a `PolicyBuilder` object configured to handle errors representing a possible transient fault:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet7)]

<span data-ttu-id="ab97c-399">위의 코드에서는 `WaitAndRetryAsync` 정책을 정의하고 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-399">In the preceding code, a `WaitAndRetryAsync` policy is defined.</span></span> <span data-ttu-id="ab97c-400">실패한 요청은 최대 세 번까지 다시 시도되며 시도 간 600밀리초의 지연 간격을 둡니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-400">Failed requests are retried up to three times with a delay of 600 ms between attempts.</span></span>

### <a name="dynamically-select-policies"></a><span data-ttu-id="ab97c-401">동적으로 정책 선택</span><span class="sxs-lookup"><span data-stu-id="ab97c-401">Dynamically select policies</span></span>

<span data-ttu-id="ab97c-402">Polly 기반 처리기를 추가하는 데 사용할 수 있는 추가 확장 메서드가 존재합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-402">Additional extension methods exist which can be used to add Polly-based handlers.</span></span> <span data-ttu-id="ab97c-403">이러한 확장 중 하나는 여러 오버로드가 있는 `AddPolicyHandler`입니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-403">One such extension is `AddPolicyHandler`, which has multiple overloads.</span></span> <span data-ttu-id="ab97c-404">한 오버로드는 적용할 정책을 정의할 때 요청을 검사할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-404">One overload allows the request to be inspected when defining which policy to apply:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

<span data-ttu-id="ab97c-405">위의 코드에서는 나가는 요청이 HTTP GET인 경우 10초 시간 제한이 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-405">In the preceding code, if the outgoing request is an HTTP GET, a 10-second timeout is applied.</span></span> <span data-ttu-id="ab97c-406">다른 HTTP 메서드의 경우 30초 시간 제한이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-406">For any other HTTP method, a 30-second timeout is used.</span></span>

### <a name="add-multiple-polly-handlers"></a><span data-ttu-id="ab97c-407">여러 Polly 처리기 추가</span><span class="sxs-lookup"><span data-stu-id="ab97c-407">Add multiple Polly handlers</span></span>

<span data-ttu-id="ab97c-408">향상된 기능을 제공하기 위해 Polly 정책을 중첩하는 것이 일반적입니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-408">It's common to nest Polly policies to provide enhanced functionality:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

<span data-ttu-id="ab97c-409">위의 예제에서는 두 개의 처리기가 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-409">In the preceding example, two handlers are added.</span></span> <span data-ttu-id="ab97c-410">첫 번째 처리기는 재시도 정책을 추가하기 위해 `AddTransientHttpErrorPolicy` 확장을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-410">The first uses the `AddTransientHttpErrorPolicy` extension to add a retry policy.</span></span> <span data-ttu-id="ab97c-411">실패한 요청은 최대 세 번까지 다시 시도됩니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-411">Failed requests are retried up to three times.</span></span> <span data-ttu-id="ab97c-412">`AddTransientHttpErrorPolicy`에 대한 두 번째 호출은 회로 차단기 정책을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-412">The second call to `AddTransientHttpErrorPolicy` adds a circuit breaker policy.</span></span> <span data-ttu-id="ab97c-413">5번의 시도가 순차적으로 실패하는 경우 추가적인 외부 요청은 30초 동안 차단됩니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-413">Further external requests are blocked for 30 seconds if five failed attempts occur sequentially.</span></span> <span data-ttu-id="ab97c-414">회로 차단기 정책은 상태를 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-414">Circuit breaker policies are stateful.</span></span> <span data-ttu-id="ab97c-415">이 클라이언트를 통한 모든 호출은 동일한 회로 상태를 공유합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-415">All calls through this client share the same circuit state.</span></span>

### <a name="add-policies-from-the-polly-registry"></a><span data-ttu-id="ab97c-416">Polly 레지스트리로부터 정책 추가</span><span class="sxs-lookup"><span data-stu-id="ab97c-416">Add policies from the Polly registry</span></span>

<span data-ttu-id="ab97c-417">정기적으로 사용되는 정책을 관리하는 방법은 정책을 한 번 정의하고 이를 `PolicyRegistry`에 등록하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-417">An approach to managing regularly used policies is to define them once and register them with a `PolicyRegistry`.</span></span> <span data-ttu-id="ab97c-418">레지스트리의 정책을 사용하여 처리기를 추가할 수 있는 확장 메서드가 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-418">An extension method is provided which allows a handler to be added using a policy from the registry:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet10)]

<span data-ttu-id="ab97c-419">위의 코드에서는 `PolicyRegistry`가 `ServiceCollection`에 추가될 때 두 가지 정책이 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-419">In the preceding code, two policies are registered when the `PolicyRegistry` is added to the `ServiceCollection`.</span></span> <span data-ttu-id="ab97c-420">레지스트리의 정책을 사용하기 위해서는 적용할 정책의 이름을 전달하여 `AddPolicyHandlerFromRegistry` 메서드를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-420">To use a policy from the registry, the `AddPolicyHandlerFromRegistry` method is used, passing the name of the policy to apply.</span></span>

<span data-ttu-id="ab97c-421">`IHttpClientFactory` 및 Polly 통합에 대한 추가 정보는 [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory)에서 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-421">Further information about `IHttpClientFactory` and Polly integrations can be found on the [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span></span>

## <a name="httpclient-and-lifetime-management"></a><span data-ttu-id="ab97c-422">HttpClient 및 수명 관리</span><span class="sxs-lookup"><span data-stu-id="ab97c-422">HttpClient and lifetime management</span></span>

<span data-ttu-id="ab97c-423">`IHttpClientFactory`에서 `CreateClient`가 호출될 때마다 새로운 `HttpClient` 인스턴스가 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-423">A new `HttpClient` instance is returned each time `CreateClient` is called on the `IHttpClientFactory`.</span></span> <span data-ttu-id="ab97c-424">명명된 클라이언트마다 <xref:System.Net.Http.HttpMessageHandler>가 존재합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-424">There's an <xref:System.Net.Http.HttpMessageHandler> per named client.</span></span> <span data-ttu-id="ab97c-425">팩터리는 `HttpMessageHandler` 인스턴스의 수명을 관리합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-425">The factory manages the lifetimes of the `HttpMessageHandler` instances.</span></span>

<span data-ttu-id="ab97c-426">`IHttpClientFactory`는 리소스 사용을 줄이기 위해 팩터리에서 만든 `HttpMessageHandler` 인스턴스를 풀링합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-426">`IHttpClientFactory` pools the `HttpMessageHandler` instances created by the factory to reduce resource consumption.</span></span> <span data-ttu-id="ab97c-427">수명이 만료되지 않은 경우, 새 `HttpClient` 인스턴스를 만들 때 풀에서 `HttpMessageHandler` 인스턴스가 재사용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-427">An `HttpMessageHandler` instance may be reused from the pool when creating a new `HttpClient` instance if its lifetime hasn't expired.</span></span>

<span data-ttu-id="ab97c-428">일반적으로 각 처리기는 자체적인 기본 HTTP 연결을 관리하므로 처리기의 풀링이 적합합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-428">Pooling of handlers is desirable as each handler typically manages its own underlying HTTP connections.</span></span> <span data-ttu-id="ab97c-429">필요한 것보다 많은 처리기를 만들면 연결 지연이 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-429">Creating more handlers than necessary can result in connection delays.</span></span> <span data-ttu-id="ab97c-430">또한 일부 처리기는 무한정으로 연결을 열어 놓아 처리기가 DNS 변경에 대응하는 것을 막을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-430">Some handlers also keep connections open indefinitely, which can prevent the handler from reacting to DNS changes.</span></span>

<span data-ttu-id="ab97c-431">기본 처리기 수명은 2분입니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-431">The default handler lifetime is two minutes.</span></span> <span data-ttu-id="ab97c-432">명명된 클라이언트별 기준으로 기본값을 재정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-432">The default value can be overridden on a per named client basis.</span></span> <span data-ttu-id="ab97c-433">이를 재정의하려면 클라이언트를 만들 때 반환되는 `IHttpClientBuilder`에서 <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*>을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-433">To override it, call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> on the `IHttpClientBuilder` that is returned when creating the client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet11)]

<span data-ttu-id="ab97c-434">클라이언트의 삭제는 불필요합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-434">Disposal of the client isn't required.</span></span> <span data-ttu-id="ab97c-435">삭제는 나가는 요청을 취소하고 <xref:System.IDisposable.Dispose*>를 호출한 후에는 지정된 `HttpClient` 인스턴스가 사용될 수 없도록 보장합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-435">Disposal cancels outgoing requests and guarantees the given `HttpClient` instance can't be used after calling <xref:System.IDisposable.Dispose*>.</span></span> <span data-ttu-id="ab97c-436">`IHttpClientFactory`는 `HttpClient` 인스턴스에서 사용되는 리소스를 추적하고 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-436">`IHttpClientFactory` tracks and disposes resources used by `HttpClient` instances.</span></span> <span data-ttu-id="ab97c-437">`HttpClient` 인스턴스는 일반적으로 삭제가 필요하지 않은 .NET 개체로 간주할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-437">The `HttpClient` instances can generally be treated as .NET objects not requiring disposal.</span></span>

<span data-ttu-id="ab97c-438">장기간 단일 `HttpClient` 인스턴스를 활성 상태로 유지하는 것은 `IHttpClientFactory`가 등장하기 전에 사용되던 일반적인 패턴입니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-438">Keeping a single `HttpClient` instance alive for a long duration is a common pattern used before the inception of `IHttpClientFactory`.</span></span> <span data-ttu-id="ab97c-439">이 패턴은 `IHttpClientFactory`로 마이그레이션한 이후에는 불필요합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-439">This pattern becomes unnecessary after migrating to `IHttpClientFactory`.</span></span>

## <a name="logging"></a><span data-ttu-id="ab97c-440">로깅</span><span class="sxs-lookup"><span data-stu-id="ab97c-440">Logging</span></span>

<span data-ttu-id="ab97c-441">`IHttpClientFactory`을 통해 만든 클라이언트는 모든 요청에 대한 로그 메시지를 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-441">Clients created via `IHttpClientFactory` record log messages for all requests.</span></span> <span data-ttu-id="ab97c-442">기본 로그 메시지를 보려면 로깅 구성에서 적절한 정보 수준을 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-442">Enable the appropriate information level in your logging configuration to see the default log messages.</span></span> <span data-ttu-id="ab97c-443">요청 헤더의 로깅 등과 같은 추가 로깅은 추적 수준에서만 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-443">Additional logging, such as the logging of request headers, is only included at trace level.</span></span>

<span data-ttu-id="ab97c-444">각 클라이언트에 사용되는 로그 범주는 클라이언트의 이름을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-444">The log category used for each client includes the name of the client.</span></span> <span data-ttu-id="ab97c-445">예를 들어, *MyNamedClient*라는 클라이언트는 `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`의 범주를 사용하여 메시지를 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-445">A client named *MyNamedClient*, for example, logs messages with a category of `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`.</span></span> <span data-ttu-id="ab97c-446">*LogicalHandler*라는 접미사가 붙은 메시지는 요청 처리기 파이프라인 외부에서 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-446">Messages suffixed with *LogicalHandler* occur outside the request handler pipeline.</span></span> <span data-ttu-id="ab97c-447">요청 시 파이프라인의 다른 모든 처리기에서 이를 처리하기 전에 메시지가 기록됩니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-447">On the request, messages are logged before any other handlers in the pipeline have processed it.</span></span> <span data-ttu-id="ab97c-448">응답 시 다른 모든 파이프라인 처리기가 응답을 받은 후에 메시지가 기록됩니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-448">On the response, messages are logged after any other pipeline handlers have received the response.</span></span>

<span data-ttu-id="ab97c-449">로깅은 요청 처리기 파이프라인 내부에서도 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-449">Logging also occurs inside the request handler pipeline.</span></span> <span data-ttu-id="ab97c-450">*MyNamedClient* 예제에서 해당 메시지는 로그 범주 `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`에 대해 기록됩니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-450">In the *MyNamedClient* example, those messages are logged against the log category `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`.</span></span> <span data-ttu-id="ab97c-451">요청의 경우 이는 요청이 네트워크에서 전송되기 직전 및 다른 모든 처리기가 실행된 후에 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-451">For the request, this occurs after all other handlers have run and immediately before the request is sent out on the network.</span></span> <span data-ttu-id="ab97c-452">응답 시 이 로깅은 처리기 파이프라인을 통해 응답이 다시 전달되기 전의 응답 상태를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-452">On the response, this logging includes the state of the response before it passes back through the handler pipeline.</span></span>

<span data-ttu-id="ab97c-453">파이프라인 외부 및 내부에서 로깅을 사용하도록 설정하면 다른 파이프라인 처리기가 수행한 변경 내용을 검사할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-453">Enabling logging outside and inside the pipeline enables inspection of the changes made by the other pipeline handlers.</span></span> <span data-ttu-id="ab97c-454">예를 들어 여기에는 요청 헤더 또는 응답 상태 코드에 대한 변경이 포함될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-454">This may include changes to request headers, for example, or to the response status code.</span></span>

<span data-ttu-id="ab97c-455">로그 범주에 클라이언트의 이름을 포함하면 필요한 경우 명명된 특정 클라이언트에 대한 로그 필터링을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-455">Including the name of the client in the log category enables log filtering for specific named clients where necessary.</span></span>

## <a name="configure-the-httpmessagehandler"></a><span data-ttu-id="ab97c-456">HttpMessageHandler 구성</span><span class="sxs-lookup"><span data-stu-id="ab97c-456">Configure the HttpMessageHandler</span></span>

<span data-ttu-id="ab97c-457">클라이언트가 사용하는 내부 `HttpMessageHandler`의 구성을 제어해야 할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-457">It may be necessary to control the configuration of the inner `HttpMessageHandler` used by a client.</span></span>

<span data-ttu-id="ab97c-458">`IHttpClientBuilder`는 명명된 또는 형식화된 클라이언트를 추가할 때 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-458">An `IHttpClientBuilder` is returned when adding named or typed clients.</span></span> <span data-ttu-id="ab97c-459"><xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> 확장 메서드는 대리자를 정의하는 데 사용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-459">The <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> extension method can be used to define a delegate.</span></span> <span data-ttu-id="ab97c-460">대리자는 해당 클라이언트가 사용하는 기본 `HttpMessageHandler`을 만들고 구성하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-460">The delegate is used to create and configure the primary `HttpMessageHandler` used by that client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet12)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a><span data-ttu-id="ab97c-461">콘솔 앱에서 IHttpClientFactory 사용</span><span class="sxs-lookup"><span data-stu-id="ab97c-461">Use IHttpClientFactory in a console app</span></span>

<span data-ttu-id="ab97c-462">콘솔 앱에서 프로젝트에 다음 패키지 참조를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-462">In a console app, add the following package references to the project:</span></span>

* [<span data-ttu-id="ab97c-463">Microsoft.Extensions.Hosting</span><span class="sxs-lookup"><span data-stu-id="ab97c-463">Microsoft.Extensions.Hosting</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [<span data-ttu-id="ab97c-464">Microsoft.Extensions.Http</span><span class="sxs-lookup"><span data-stu-id="ab97c-464">Microsoft.Extensions.Http</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Http)

<span data-ttu-id="ab97c-465">다음 예제에서는</span><span class="sxs-lookup"><span data-stu-id="ab97c-465">In the following example:</span></span>

* <span data-ttu-id="ab97c-466"><xref:System.Net.Http.IHttpClientFactory>는 [제너릭 호스트의](xref:fundamentals/host/generic-host) 서비스 컨테이너에 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-466"><xref:System.Net.Http.IHttpClientFactory> is registered in the [Generic Host's](xref:fundamentals/host/generic-host) service container.</span></span>
* <span data-ttu-id="ab97c-467">`MyService`에서는 `HttpClient`를 만드는 데 사용하는 서비스에서 클라이언트 팩터리 인스턴스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-467">`MyService` creates a client factory instance from the service, which is used to create an `HttpClient`.</span></span> <span data-ttu-id="ab97c-468">`HttpClient`는 웹 페이지를 검색하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-468">`HttpClient` is used to retrieve a webpage.</span></span>
* <span data-ttu-id="ab97c-469">`Main`을 통해서는 서비스의 `GetPage` 메서드를 실행하고 웹 페이지 콘텐츠의 처음 500자를 콘솔에 씁니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-469">`Main` creates a scope to execute the service's `GetPage` method and write the first 500 characters of the webpage content to the console.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="additional-resources"></a><span data-ttu-id="ab97c-470">추가 자료</span><span class="sxs-lookup"><span data-stu-id="ab97c-470">Additional resources</span></span>

* [<span data-ttu-id="ab97c-471">HttpClientFactory를 사용하여 복원력 있는 HTTP 요청 구현</span><span class="sxs-lookup"><span data-stu-id="ab97c-471">Use HttpClientFactory to implement resilient HTTP requests</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [<span data-ttu-id="ab97c-472">HttpClientFactory 및 Polly 정책을 통해 지수 백오프를 사용하여 HTTP 호출 다시 시도 구현</span><span class="sxs-lookup"><span data-stu-id="ab97c-472">Implement HTTP call retries with exponential backoff with HttpClientFactory and Polly policies</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [<span data-ttu-id="ab97c-473">회로 차단기 패턴 구현</span><span class="sxs-lookup"><span data-stu-id="ab97c-473">Implement the Circuit Breaker pattern</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)

::: moniker-end

::: moniker range="<= aspnetcore-2.1"

<span data-ttu-id="ab97c-474">작성자: [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak) 및 [Steve Gordon](https://github.com/stevejgordon)</span><span class="sxs-lookup"><span data-stu-id="ab97c-474">By [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak), and [Steve Gordon](https://github.com/stevejgordon)</span></span>

<span data-ttu-id="ab97c-475">앱에서 <xref:System.Net.Http.IHttpClientFactory>를 등록하여 <xref:System.Net.Http.HttpClient> 인스턴스를 구성하고 만드는 데 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-475">An <xref:System.Net.Http.IHttpClientFactory> can be registered and used to configure and create <xref:System.Net.Http.HttpClient> instances in an app.</span></span> <span data-ttu-id="ab97c-476">이는 다음과 같은 이점을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-476">It offers the following benefits:</span></span>

* <span data-ttu-id="ab97c-477">논리적 `HttpClient` 인스턴스를 구성하고 이름을 지정하기 위한 중앙 위치를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-477">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="ab97c-478">예를 들어, [GitHub](https://github.com/)에 액세스하는 *github* 클라이언트를 등록 및 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-478">For example, a *github* client can be registered and configured to access [GitHub](https://github.com/).</span></span> <span data-ttu-id="ab97c-479">기본 클라이언트는 다른 용도로 등록할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-479">A default client can be registered for other purposes.</span></span>
* <span data-ttu-id="ab97c-480">`HttpClient`에서 위임 처리기를 통해 나가는 미들웨어의 개념을 체계화하고 Polly 기반 미들웨어에 대한 확장을 제공하여 이를 활용합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-480">Codifies the concept of outgoing middleware via delegating handlers in `HttpClient` and provides extensions for Polly-based middleware to take advantage of that.</span></span>
* <span data-ttu-id="ab97c-481">`HttpClient` 수명을 수동으로 관리할 때 발생하는 일반적인 DNS 문제를 피하기 위해 기본 `HttpClientMessageHandler` 인스턴스의 풀링 및 수명을 관리합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-481">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances to avoid common DNS problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="ab97c-482">팩터리에서 만든 클라이언트를 통해 전송된 모든 요청에 대해 구성 가능한 로깅 경험(`ILogger`을 통해)을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-482">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="ab97c-483">[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="ab97c-483">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="ab97c-484">전제 조건</span><span class="sxs-lookup"><span data-stu-id="ab97c-484">Prerequisites</span></span>

<span data-ttu-id="ab97c-485">.NET Framework를 대상으로 하는 프로젝트는 [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) NuGet 패키지를 설치해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-485">Projects targeting .NET Framework require installation of the [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) NuGet package.</span></span> <span data-ttu-id="ab97c-486">.NET Core를 대상으로 하고 [Microsoft.AspNetCore.App 메타패키지](xref:fundamentals/metapackage-app)를 참조하는 프로젝트는 이미 `Microsoft.Extensions.Http` 패키지를 포함하고 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-486">Projects that target .NET Core and reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) already include the `Microsoft.Extensions.Http` package.</span></span>

## <a name="consumption-patterns"></a><span data-ttu-id="ab97c-487">사용 패턴</span><span class="sxs-lookup"><span data-stu-id="ab97c-487">Consumption patterns</span></span>

<span data-ttu-id="ab97c-488">앱에서 `IHttpClientFactory`를 사용할 수 있는 몇 가지 방법이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-488">There are several ways `IHttpClientFactory` can be used in an app:</span></span>

* [<span data-ttu-id="ab97c-489">기본적인 사용 방법</span><span class="sxs-lookup"><span data-stu-id="ab97c-489">Basic usage</span></span>](#basic-usage)
* [<span data-ttu-id="ab97c-490">명명된 클라이언트</span><span class="sxs-lookup"><span data-stu-id="ab97c-490">Named clients</span></span>](#named-clients)
* [<span data-ttu-id="ab97c-491">형식화된 클라이언트</span><span class="sxs-lookup"><span data-stu-id="ab97c-491">Typed clients</span></span>](#typed-clients)
* [<span data-ttu-id="ab97c-492">생성된 클라이언트</span><span class="sxs-lookup"><span data-stu-id="ab97c-492">Generated clients</span></span>](#generated-clients)

<span data-ttu-id="ab97c-493">어떤 방법도 다른 방법에 비해 절대적으로 우수하지는 않습니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-493">None of them are strictly superior to another.</span></span> <span data-ttu-id="ab97c-494">가장 좋은 방법은 앱의 제약 조건에 따라서 달라집니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-494">The best approach depends upon the app's constraints.</span></span>

### <a name="basic-usage"></a><span data-ttu-id="ab97c-495">기본적인 사용 방법</span><span class="sxs-lookup"><span data-stu-id="ab97c-495">Basic usage</span></span>

<span data-ttu-id="ab97c-496">`IHttpClientFactory`는 `Startup.ConfigureServices` 메서드 내에서 `IServiceCollection`의 `AddHttpClient` 확장 메서드를 호출하여 등록할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-496">The `IHttpClientFactory` can be registered by calling the `AddHttpClient` extension method on the `IServiceCollection`, inside the `Startup.ConfigureServices` method.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

<span data-ttu-id="ab97c-497">일단 등록하고 나면 코드는 [DI(종속성 주입)](xref:fundamentals/dependency-injection)를 사용하여 서비스를 주입할 수 있는 모든 곳에서 `IHttpClientFactory`를 받을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-497">Once registered, code can accept an `IHttpClientFactory` anywhere services can be injected with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="ab97c-498">`IHttpClientFactory`는 `HttpClient` 인스턴스를 만드는 데 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-498">The `IHttpClientFactory` can be used to create a `HttpClient` instance:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

<span data-ttu-id="ab97c-499">이러한 방식으로 `IHttpClientFactory`를 사용하는 것은 기존 앱을 리팩터링할 수 있는 좋은 방법입니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-499">Using `IHttpClientFactory` in this fashion is a good way to refactor an existing app.</span></span> <span data-ttu-id="ab97c-500">`HttpClient`가 사용되는 방식에는 어떠한 영향도 없습니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-500">It has no impact on the way `HttpClient` is used.</span></span> <span data-ttu-id="ab97c-501">`HttpClient` 인스턴스가 현재 만들어지는 위치에서 해당 코드를 <xref:System.Net.Http.IHttpClientFactory.CreateClient*>에 대한 호출로 대체합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-501">In places where `HttpClient` instances are currently created, replace those occurrences with a call to <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span></span>

### <a name="named-clients"></a><span data-ttu-id="ab97c-502">명명된 클라이언트</span><span class="sxs-lookup"><span data-stu-id="ab97c-502">Named clients</span></span>

<span data-ttu-id="ab97c-503">앱이 각각 다른 구성을 사용하는 많은 개별적인 `HttpClient`의 사용을 필요로 하는 경우 선택할 수 있는 방법은 **명명된 클라이언트**를 사용하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-503">If an app requires many distinct uses of `HttpClient`, each with a different configuration, an option is to use **named clients**.</span></span> <span data-ttu-id="ab97c-504">명명된 `HttpClient`에 대한 구성은 `Startup.ConfigureServices`에서 등록하는 동안 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-504">Configuration for a named `HttpClient` can be specified during registration in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

<span data-ttu-id="ab97c-505">위의 코드에서는 `AddHttpClient`를 호출하여 *github*이라는 이름을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-505">In the preceding code, `AddHttpClient` is called, providing the name *github*.</span></span> <span data-ttu-id="ab97c-506">이 클라이언트에는 일부 기본 구성&mdash;즉 GitHub API를 작동하는 데 필요한 기준 주소 및 두 개의 헤더가 적용되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-506">This client has some default configuration applied&mdash;namely the base address and two headers required to work with the GitHub API.</span></span>

<span data-ttu-id="ab97c-507">`CreateClient`를 호출할 때마다 `HttpClient`의 새 인스턴스를 만들고 구성 작업을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-507">Each time `CreateClient` is called, a new instance of `HttpClient` is created and the configuration action is called.</span></span>

<span data-ttu-id="ab97c-508">명명된 클라이언트를 사용하기 위해서 `CreateClient`에 문자열 매개 변수를 전달할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-508">To consume a named client, a string parameter can be passed to `CreateClient`.</span></span> <span data-ttu-id="ab97c-509">만들 클라이언트의 이름을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-509">Specify the name of the client to be created:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

<span data-ttu-id="ab97c-510">위의 코드에서는 요청이 호스트 이름을 지정할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-510">In the preceding code, the request doesn't need to specify a hostname.</span></span> <span data-ttu-id="ab97c-511">클라이언트에 대해 구성된 기본 주소를 사용하므로 경로만 전달할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-511">It can pass just the path, since the base address configured for the client is used.</span></span>

### <a name="typed-clients"></a><span data-ttu-id="ab97c-512">형식화된 클라이언트</span><span class="sxs-lookup"><span data-stu-id="ab97c-512">Typed clients</span></span>

<span data-ttu-id="ab97c-513">형식화된 클라이언트:</span><span class="sxs-lookup"><span data-stu-id="ab97c-513">Typed clients:</span></span>

* <span data-ttu-id="ab97c-514">문자열을 키로 사용할 필요가 없이 명명된 클라이언트와 동일한 기능을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-514">Provide the same capabilities as named clients without the need to use strings as keys.</span></span>
* <span data-ttu-id="ab97c-515">클라이언트를 사용할 때 IntelliSense 및 컴파일러 도움말을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-515">Provides IntelliSense and compiler help when consuming clients.</span></span>
* <span data-ttu-id="ab97c-516">특정 `HttpClient`을 구성하고 상호 작용하기 위해 단일 위치를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-516">Provide a single location to configure and interact with a particular `HttpClient`.</span></span> <span data-ttu-id="ab97c-517">예를 들어 단일 형식화된 클라이언트는 단일 백 엔드 엔드포인트에 사용될 수 있으며 해당 엔드포인트를 다루는 모든 논리를 캡슐화할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-517">For example, a single typed client might be used for a single backend endpoint and encapsulate all logic dealing with that endpoint.</span></span>
* <span data-ttu-id="ab97c-518">DI로 작업하고 앱에서 필요할 경우 삽입할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-518">Work with DI and can be injected where required in your app.</span></span>

<span data-ttu-id="ab97c-519">형식화된 클라이언트는 생성자에서 `HttpClient` 매개 변수를 받습니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-519">A typed client accepts a `HttpClient` parameter in its constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]

<span data-ttu-id="ab97c-520">위의 코드에서는 구성이 형식화된 클라이언트로 이동되었습니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-520">In the preceding code, the configuration is moved into the typed client.</span></span> <span data-ttu-id="ab97c-521">`HttpClient` 개체는 공용 속성으로 노출됩니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-521">The `HttpClient` object is exposed as a public property.</span></span> <span data-ttu-id="ab97c-522">`HttpClient` 기능을 노출하는 API 특정 메서드를 정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-522">It's possible to define API-specific methods that expose `HttpClient` functionality.</span></span> <span data-ttu-id="ab97c-523">`GetAspNetDocsIssues` 메서드는 GitHub 리포지토리에서 공개된 최신 문제를 구문 분석하고 쿼리하는 데 필요한 코드를 캡슐화합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-523">The `GetAspNetDocsIssues` method encapsulates the code needed to query for and parse out the latest open issues from a GitHub repository.</span></span>

<span data-ttu-id="ab97c-524">형식화된 클라이언트를 등록하기 위해서 `Startup.ConfigureServices`에서 형식화된 클라이언트 클래스를 지정하여 제네릭 <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> 확장 메서드를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-524">To register a typed client, the generic <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> extension method can be used within `Startup.ConfigureServices`, specifying the typed client class:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

<span data-ttu-id="ab97c-525">형식화된 클라이언트는 DI를 사용하여 일시적으로 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-525">The typed client is registered as transient with DI.</span></span> <span data-ttu-id="ab97c-526">형식화된 클라이언트는 직접 주입되고 사용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-526">The typed client can be injected and consumed directly:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

<span data-ttu-id="ab97c-527">원한다면 형식화된 클라이언트의 생성자 대신 `Startup.ConfigureServices`에서 등록하는 동안 형식화된 클라이언트에 대한 구성을 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-527">If preferred, the configuration for a typed client can be specified during registration in `Startup.ConfigureServices`, rather than in the typed client's constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

<span data-ttu-id="ab97c-528">형식화된 클라이언트 내에서 `HttpClient`를 완전히 캡슐화할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-528">It's possible to entirely encapsulate the `HttpClient` within a typed client.</span></span> <span data-ttu-id="ab97c-529">속성으로 노출하는 대신 공용 메서드를 제공하여 내부적으로 `HttpClient` 인스턴스를 호출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-529">Rather than exposing it as a property, public methods can be provided which call the `HttpClient` instance internally.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

<span data-ttu-id="ab97c-530">위의 코드에서는 `HttpClient`가 전용 필드로 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-530">In the preceding code, the `HttpClient` is stored as a private field.</span></span> <span data-ttu-id="ab97c-531">외부 호출을 하기 위한 모든 액세스는 `GetRepos` 메서드를 거칩니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-531">All access to make external calls goes through the `GetRepos` method.</span></span>

### <a name="generated-clients"></a><span data-ttu-id="ab97c-532">생성된 클라이언트</span><span class="sxs-lookup"><span data-stu-id="ab97c-532">Generated clients</span></span>

<span data-ttu-id="ab97c-533">`IHttpClientFactory`는 [Refit](https://github.com/paulcbetts/refit)과 같은 다른 타사 라이브러리와 함께 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-533">`IHttpClientFactory` can be used in combination with other third-party libraries such as [Refit](https://github.com/paulcbetts/refit).</span></span> <span data-ttu-id="ab97c-534">Refit은 .NET용 REST 라이브러리입니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-534">Refit is a REST library for .NET.</span></span> <span data-ttu-id="ab97c-535">REST API를 라이브 인터페이스로 변환합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-535">It converts REST APIs into live interfaces.</span></span> <span data-ttu-id="ab97c-536">인터페이스의 구현은 `HttpClient`를 사용하여 외부 HTTP를 호출하도록 `RestService`에 의해 동적으로 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-536">An implementation of the interface is generated dynamically by the `RestService`, using `HttpClient` to make the external HTTP calls.</span></span>

<span data-ttu-id="ab97c-537">외부 API와 해당 응답을 나타내기 위한 인터페이스와 회신이 정의됩니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-537">An interface and a reply are defined to represent the external API and its response:</span></span>

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

<span data-ttu-id="ab97c-538">구현을 생성하기 위해 Refit를 사용하여 형식화된 클라이언트를 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-538">A typed client can be added, using Refit to generate the implementation:</span></span>

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

<span data-ttu-id="ab97c-539">DI 및 Refit에서 제공한 구현을 통해 필요한 곳에서 정의된 인터페이스를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-539">The defined interface can be consumed where necessary, with the implementation provided by DI and Refit:</span></span>

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

## <a name="outgoing-request-middleware"></a><span data-ttu-id="ab97c-540">나가는 요청 미들웨어</span><span class="sxs-lookup"><span data-stu-id="ab97c-540">Outgoing request middleware</span></span>

<span data-ttu-id="ab97c-541">`HttpClient`에는 나가는 HTTP 요청을 위해 함께 연결될 수 있는 위임 처리기라는 개념이 이미 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-541">`HttpClient` already has the concept of delegating handlers that can be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="ab97c-542">`IHttpClientFactory`를 사용하면 각 명명된 클라이언트에 적용할 처리기를 쉽게 정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-542">The `IHttpClientFactory` makes it easy to define the handlers to apply for each named client.</span></span> <span data-ttu-id="ab97c-543">나가는 요청 미들웨어 파이프라인을 만들기 위한 여러 처리기의 등록 및 연결을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-543">It supports registration and chaining of multiple handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="ab97c-544">이러한 처리기 각각은 나가는 요청 전후에 작업을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-544">Each of these handlers is able to perform work before and after the outgoing request.</span></span> <span data-ttu-id="ab97c-545">이 패턴은 ASP.NET Core의 인바운드 미들웨어 파이프라인과 비슷합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-545">This pattern is similar to the inbound middleware pipeline in ASP.NET Core.</span></span> <span data-ttu-id="ab97c-546">이 패턴은 캐싱, 오류 처리, 직렬화 및 로깅을 포함한 HTTP 요청을 둘러싼 횡단 관심사를 관리하기 위한 메커니즘을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-546">The pattern provides a mechanism to manage cross-cutting concerns around HTTP requests, including caching, error handling, serialization, and logging.</span></span>

<span data-ttu-id="ab97c-547">처리기를 만들려면 <xref:System.Net.Http.DelegatingHandler>에서 파생되는 클래스를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-547">To create a handler, define a class deriving from <xref:System.Net.Http.DelegatingHandler>.</span></span> <span data-ttu-id="ab97c-548">파이프라인의 다음 처리기로 요청을 전달하기 전에 코드를 실행하려면 `SendAsync` 메서드를 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-548">Override the `SendAsync` method to execute code before passing the request to the next handler in the pipeline:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

<span data-ttu-id="ab97c-549">위의 코드에서는 기본 처리기를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-549">The preceding code defines a basic handler.</span></span> <span data-ttu-id="ab97c-550">`X-API-KEY` 헤더가 요청에 포함되었는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-550">It checks to see if an `X-API-KEY` header has been included on the request.</span></span> <span data-ttu-id="ab97c-551">헤더가 누락된 경우 HTTP 호출을 방지하고 적합한 응답을 반환할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-551">If the header is missing, it can avoid the HTTP call and return a suitable response.</span></span>

<span data-ttu-id="ab97c-552">등록하는 동안 하나 이상의 처리기가 `HttpClient`의 구성에 추가될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-552">During registration, one or more handlers can be added to the configuration for a `HttpClient`.</span></span> <span data-ttu-id="ab97c-553">이 작업은 <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>의 확장 메서드를 통해 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-553">This task is accomplished via extension methods on the <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet5)]

<span data-ttu-id="ab97c-554">위의 코드에서 `ValidateHeaderHandler`는 DI에 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-554">In the preceding code, the `ValidateHeaderHandler` is registered with DI.</span></span> <span data-ttu-id="ab97c-555">처리기는 범위가 지정되지 않은, 임시 서비스로 DI에 등록**되어야** 합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-555">The handler **must** be registered in DI as a transient service, never scoped.</span></span> <span data-ttu-id="ab97c-556">처리기가 범위 지정 서비스로 등록되고 처리기가 종속된 모든 서비스는 삭제 가능합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-556">If the handler is registered as a scoped service and any services that the handler depends upon are disposable:</span></span>

* <span data-ttu-id="ab97c-557">처리기가 범위를 벗어나기 전에 처리기의 서비스를 삭제할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-557">The handler's services could be disposed before the handler goes out of scope.</span></span>
* <span data-ttu-id="ab97c-558">처리기 서비스를 삭제하면 처리기가 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-558">The disposed handler services causes the handler to fail.</span></span>

<span data-ttu-id="ab97c-559">일단 등록되면 처리기 형식으로 전달하여 <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*>를 호출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-559">Once registered, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> can be called, passing in the handler type.</span></span>

<span data-ttu-id="ab97c-560">실행해야 하는 순서에 따라 여러 처리기를 등록할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-560">Multiple handlers can be registered in the order that they should execute.</span></span> <span data-ttu-id="ab97c-561">각 처리기는 최종 `HttpClientHandler`가 요청을 실행할 때까지 다음 처리기를 래핑합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-561">Each handler wraps the next handler until the final `HttpClientHandler` executes the request:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

<span data-ttu-id="ab97c-562">다음 방법 중 하나를 사용하여 메시지 처리기와 요청별 상태를 공유하세요.</span><span class="sxs-lookup"><span data-stu-id="ab97c-562">Use one of the following approaches to share per-request state with message handlers:</span></span>

* <span data-ttu-id="ab97c-563">`HttpRequestMessage.Properties`를 사용하여 데이터를 처리기로 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-563">Pass data into the handler using `HttpRequestMessage.Properties`.</span></span>
* <span data-ttu-id="ab97c-564">`IHttpContextAccessor`를 사용하여 현재 요청에 액세스합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-564">Use `IHttpContextAccessor` to access the current request.</span></span>
* <span data-ttu-id="ab97c-565">사용자 지정 `AsyncLocal` 스토리지 개체를 만들어 데이터를 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-565">Create a custom `AsyncLocal` storage object to pass the data.</span></span>

## <a name="use-polly-based-handlers"></a><span data-ttu-id="ab97c-566">Polly 기반 처리기 사용</span><span class="sxs-lookup"><span data-stu-id="ab97c-566">Use Polly-based handlers</span></span>

<span data-ttu-id="ab97c-567">`IHttpClientFactory`는 [Polly](https://github.com/App-vNext/Polly)라는 유명한 타사 라이브러리와 통합됩니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-567">`IHttpClientFactory` integrates with a popular third-party library called [Polly](https://github.com/App-vNext/Polly).</span></span> <span data-ttu-id="ab97c-568">Polly는 .NET용 포괄적인 회복탄력성 및 일시적 오류 처리 라이브러리입니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-568">Polly is a comprehensive resilience and transient fault-handling library for .NET.</span></span> <span data-ttu-id="ab97c-569">개발자는 이를 사용하여 재시도, 회로 차단기, 시간 초과, 격벽 격리 및 대체(Fallback) 같은 정책을 유연하고 스레드로부터 안전한 방식으로 표현할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-569">It allows developers to express policies such as Retry, Circuit Breaker, Timeout, Bulkhead Isolation, and Fallback in a fluent and thread-safe manner.</span></span>

<span data-ttu-id="ab97c-570">구성된 `HttpClient` 인스턴스를 통해 Polly 정책을 사용할 수 있도록 확장 메서드가 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-570">Extension methods are provided to enable the use of Polly policies with configured `HttpClient` instances.</span></span> <span data-ttu-id="ab97c-571">Polly 확장은:</span><span class="sxs-lookup"><span data-stu-id="ab97c-571">The Polly extensions:</span></span>

* <span data-ttu-id="ab97c-572">클라이언트에 Polly 기반 처리기 추가를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-572">Support adding Polly-based handlers to clients.</span></span>
* <span data-ttu-id="ab97c-573">[Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet 패키지를 설치한 후 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-573">Can be used after installing the [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet package.</span></span> <span data-ttu-id="ab97c-574">이 패키지는 ASP.NET Core 공유 프레임워크에 포함되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-574">The package isn't included in the ASP.NET Core shared framework.</span></span>

### <a name="handle-transient-faults"></a><span data-ttu-id="ab97c-575">일시적인 오류 처리</span><span class="sxs-lookup"><span data-stu-id="ab97c-575">Handle transient faults</span></span>

<span data-ttu-id="ab97c-576">가장 일반적인 오류는 외부 HTTP 호출이 일시적인 경우 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-576">Most common faults occur when external HTTP calls are transient.</span></span> <span data-ttu-id="ab97c-577">일시적인 오류를 처리하기 위한 정책을 정의할 수 있는 `AddTransientHttpErrorPolicy`라는 편리한 확장 메서드가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-577">A convenient extension method called `AddTransientHttpErrorPolicy` is included which allows a policy to be defined to handle transient errors.</span></span> <span data-ttu-id="ab97c-578">이 확장 메서드로 구성된 정책은 `HttpRequestException`, HTTP 5xx 응답 및 HTTP 408 응답을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-578">Policies configured with this extension method handle `HttpRequestException`, HTTP 5xx responses, and HTTP 408 responses.</span></span>

<span data-ttu-id="ab97c-579">`AddTransientHttpErrorPolicy` 확장은 `Startup.ConfigureServices` 내에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-579">The `AddTransientHttpErrorPolicy` extension can be used within `Startup.ConfigureServices`.</span></span> <span data-ttu-id="ab97c-580">이 확장은 가능한 일시적 오류를 나타내는 오류를 처리하기 위해 구성된 `PolicyBuilder` 개체에 대한 액세스를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-580">The extension provides access to a `PolicyBuilder` object configured to handle errors representing a possible transient fault:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet7)]

<span data-ttu-id="ab97c-581">위의 코드에서는 `WaitAndRetryAsync` 정책을 정의하고 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-581">In the preceding code, a `WaitAndRetryAsync` policy is defined.</span></span> <span data-ttu-id="ab97c-582">실패한 요청은 최대 세 번까지 다시 시도되며 시도 간 600밀리초의 지연 간격을 둡니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-582">Failed requests are retried up to three times with a delay of 600 ms between attempts.</span></span>

### <a name="dynamically-select-policies"></a><span data-ttu-id="ab97c-583">동적으로 정책 선택</span><span class="sxs-lookup"><span data-stu-id="ab97c-583">Dynamically select policies</span></span>

<span data-ttu-id="ab97c-584">Polly 기반 처리기를 추가하는 데 사용할 수 있는 추가 확장 메서드가 존재합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-584">Additional extension methods exist which can be used to add Polly-based handlers.</span></span> <span data-ttu-id="ab97c-585">이러한 확장 중 하나는 여러 오버로드가 있는 `AddPolicyHandler`입니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-585">One such extension is `AddPolicyHandler`, which has multiple overloads.</span></span> <span data-ttu-id="ab97c-586">한 오버로드는 적용할 정책을 정의할 때 요청을 검사할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-586">One overload allows the request to be inspected when defining which policy to apply:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

<span data-ttu-id="ab97c-587">위의 코드에서는 나가는 요청이 HTTP GET인 경우 10초 시간 제한이 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-587">In the preceding code, if the outgoing request is an HTTP GET, a 10-second timeout is applied.</span></span> <span data-ttu-id="ab97c-588">다른 HTTP 메서드의 경우 30초 시간 제한이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-588">For any other HTTP method, a 30-second timeout is used.</span></span>

### <a name="add-multiple-polly-handlers"></a><span data-ttu-id="ab97c-589">여러 Polly 처리기 추가</span><span class="sxs-lookup"><span data-stu-id="ab97c-589">Add multiple Polly handlers</span></span>

<span data-ttu-id="ab97c-590">향상된 기능을 제공하기 위해 Polly 정책을 중첩하는 것이 일반적입니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-590">It's common to nest Polly policies to provide enhanced functionality:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

<span data-ttu-id="ab97c-591">위의 예제에서는 두 개의 처리기가 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-591">In the preceding example, two handlers are added.</span></span> <span data-ttu-id="ab97c-592">첫 번째 처리기는 재시도 정책을 추가하기 위해 `AddTransientHttpErrorPolicy` 확장을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-592">The first uses the `AddTransientHttpErrorPolicy` extension to add a retry policy.</span></span> <span data-ttu-id="ab97c-593">실패한 요청은 최대 세 번까지 다시 시도됩니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-593">Failed requests are retried up to three times.</span></span> <span data-ttu-id="ab97c-594">`AddTransientHttpErrorPolicy`에 대한 두 번째 호출은 회로 차단기 정책을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-594">The second call to `AddTransientHttpErrorPolicy` adds a circuit breaker policy.</span></span> <span data-ttu-id="ab97c-595">5번의 시도가 순차적으로 실패하는 경우 추가적인 외부 요청은 30초 동안 차단됩니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-595">Further external requests are blocked for 30 seconds if five failed attempts occur sequentially.</span></span> <span data-ttu-id="ab97c-596">회로 차단기 정책은 상태를 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-596">Circuit breaker policies are stateful.</span></span> <span data-ttu-id="ab97c-597">이 클라이언트를 통한 모든 호출은 동일한 회로 상태를 공유합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-597">All calls through this client share the same circuit state.</span></span>

### <a name="add-policies-from-the-polly-registry"></a><span data-ttu-id="ab97c-598">Polly 레지스트리로부터 정책 추가</span><span class="sxs-lookup"><span data-stu-id="ab97c-598">Add policies from the Polly registry</span></span>

<span data-ttu-id="ab97c-599">정기적으로 사용되는 정책을 관리하는 방법은 정책을 한 번 정의하고 이를 `PolicyRegistry`에 등록하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-599">An approach to managing regularly used policies is to define them once and register them with a `PolicyRegistry`.</span></span> <span data-ttu-id="ab97c-600">레지스트리의 정책을 사용하여 처리기를 추가할 수 있는 확장 메서드가 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-600">An extension method is provided which allows a handler to be added using a policy from the registry:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet10)]

<span data-ttu-id="ab97c-601">위의 코드에서는 `PolicyRegistry`가 `ServiceCollection`에 추가될 때 두 가지 정책이 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-601">In the preceding code, two policies are registered when the `PolicyRegistry` is added to the `ServiceCollection`.</span></span> <span data-ttu-id="ab97c-602">레지스트리의 정책을 사용하기 위해서는 적용할 정책의 이름을 전달하여 `AddPolicyHandlerFromRegistry` 메서드를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-602">To use a policy from the registry, the `AddPolicyHandlerFromRegistry` method is used, passing the name of the policy to apply.</span></span>

<span data-ttu-id="ab97c-603">`IHttpClientFactory` 및 Polly 통합에 대한 추가 정보는 [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory)에서 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-603">Further information about `IHttpClientFactory` and Polly integrations can be found on the [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span></span>

## <a name="httpclient-and-lifetime-management"></a><span data-ttu-id="ab97c-604">HttpClient 및 수명 관리</span><span class="sxs-lookup"><span data-stu-id="ab97c-604">HttpClient and lifetime management</span></span>

<span data-ttu-id="ab97c-605">`IHttpClientFactory`에서 `CreateClient`가 호출될 때마다 새로운 `HttpClient` 인스턴스가 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-605">A new `HttpClient` instance is returned each time `CreateClient` is called on the `IHttpClientFactory`.</span></span> <span data-ttu-id="ab97c-606">명명된 클라이언트마다 <xref:System.Net.Http.HttpMessageHandler>가 존재합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-606">There's an <xref:System.Net.Http.HttpMessageHandler> per named client.</span></span> <span data-ttu-id="ab97c-607">팩터리는 `HttpMessageHandler` 인스턴스의 수명을 관리합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-607">The factory manages the lifetimes of the `HttpMessageHandler` instances.</span></span>

<span data-ttu-id="ab97c-608">`IHttpClientFactory`는 리소스 사용을 줄이기 위해 팩터리에서 만든 `HttpMessageHandler` 인스턴스를 풀링합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-608">`IHttpClientFactory` pools the `HttpMessageHandler` instances created by the factory to reduce resource consumption.</span></span> <span data-ttu-id="ab97c-609">수명이 만료되지 않은 경우, 새 `HttpClient` 인스턴스를 만들 때 풀에서 `HttpMessageHandler` 인스턴스가 재사용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-609">An `HttpMessageHandler` instance may be reused from the pool when creating a new `HttpClient` instance if its lifetime hasn't expired.</span></span>

<span data-ttu-id="ab97c-610">일반적으로 각 처리기는 자체적인 기본 HTTP 연결을 관리하므로 처리기의 풀링이 적합합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-610">Pooling of handlers is desirable as each handler typically manages its own underlying HTTP connections.</span></span> <span data-ttu-id="ab97c-611">필요한 것보다 많은 처리기를 만들면 연결 지연이 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-611">Creating more handlers than necessary can result in connection delays.</span></span> <span data-ttu-id="ab97c-612">또한 일부 처리기는 무한정으로 연결을 열어 놓아 처리기가 DNS 변경에 대응하는 것을 막을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-612">Some handlers also keep connections open indefinitely, which can prevent the handler from reacting to DNS changes.</span></span>

<span data-ttu-id="ab97c-613">기본 처리기 수명은 2분입니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-613">The default handler lifetime is two minutes.</span></span> <span data-ttu-id="ab97c-614">명명된 클라이언트별 기준으로 기본값을 재정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-614">The default value can be overridden on a per named client basis.</span></span> <span data-ttu-id="ab97c-615">이를 재정의하려면 클라이언트를 만들 때 반환되는 `IHttpClientBuilder`에서 <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*>을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-615">To override it, call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> on the `IHttpClientBuilder` that is returned when creating the client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet11)]

<span data-ttu-id="ab97c-616">클라이언트의 삭제는 불필요합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-616">Disposal of the client isn't required.</span></span> <span data-ttu-id="ab97c-617">삭제는 나가는 요청을 취소하고 <xref:System.IDisposable.Dispose*>를 호출한 후에는 지정된 `HttpClient` 인스턴스가 사용될 수 없도록 보장합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-617">Disposal cancels outgoing requests and guarantees the given `HttpClient` instance can't be used after calling <xref:System.IDisposable.Dispose*>.</span></span> <span data-ttu-id="ab97c-618">`IHttpClientFactory`는 `HttpClient` 인스턴스에서 사용되는 리소스를 추적하고 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-618">`IHttpClientFactory` tracks and disposes resources used by `HttpClient` instances.</span></span> <span data-ttu-id="ab97c-619">`HttpClient` 인스턴스는 일반적으로 삭제가 필요하지 않은 .NET 개체로 간주할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-619">The `HttpClient` instances can generally be treated as .NET objects not requiring disposal.</span></span>

<span data-ttu-id="ab97c-620">장기간 단일 `HttpClient` 인스턴스를 활성 상태로 유지하는 것은 `IHttpClientFactory`가 등장하기 전에 사용되던 일반적인 패턴입니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-620">Keeping a single `HttpClient` instance alive for a long duration is a common pattern used before the inception of `IHttpClientFactory`.</span></span> <span data-ttu-id="ab97c-621">이 패턴은 `IHttpClientFactory`로 마이그레이션한 이후에는 불필요합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-621">This pattern becomes unnecessary after migrating to `IHttpClientFactory`.</span></span>

## <a name="logging"></a><span data-ttu-id="ab97c-622">로깅</span><span class="sxs-lookup"><span data-stu-id="ab97c-622">Logging</span></span>

<span data-ttu-id="ab97c-623">`IHttpClientFactory`을 통해 만든 클라이언트는 모든 요청에 대한 로그 메시지를 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-623">Clients created via `IHttpClientFactory` record log messages for all requests.</span></span> <span data-ttu-id="ab97c-624">기본 로그 메시지를 보려면 로깅 구성에서 적절한 정보 수준을 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-624">Enable the appropriate information level in your logging configuration to see the default log messages.</span></span> <span data-ttu-id="ab97c-625">요청 헤더의 로깅 등과 같은 추가 로깅은 추적 수준에서만 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-625">Additional logging, such as the logging of request headers, is only included at trace level.</span></span>

<span data-ttu-id="ab97c-626">각 클라이언트에 사용되는 로그 범주는 클라이언트의 이름을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-626">The log category used for each client includes the name of the client.</span></span> <span data-ttu-id="ab97c-627">예를 들어, *MyNamedClient*라는 클라이언트는 `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`의 범주를 사용하여 메시지를 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-627">A client named *MyNamedClient*, for example, logs messages with a category of `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`.</span></span> <span data-ttu-id="ab97c-628">*LogicalHandler*라는 접미사가 붙은 메시지는 요청 처리기 파이프라인 외부에서 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-628">Messages suffixed with *LogicalHandler* occur outside the request handler pipeline.</span></span> <span data-ttu-id="ab97c-629">요청 시 파이프라인의 다른 모든 처리기에서 이를 처리하기 전에 메시지가 기록됩니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-629">On the request, messages are logged before any other handlers in the pipeline have processed it.</span></span> <span data-ttu-id="ab97c-630">응답 시 다른 모든 파이프라인 처리기가 응답을 받은 후에 메시지가 기록됩니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-630">On the response, messages are logged after any other pipeline handlers have received the response.</span></span>

<span data-ttu-id="ab97c-631">로깅은 요청 처리기 파이프라인 내부에서도 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-631">Logging also occurs inside the request handler pipeline.</span></span> <span data-ttu-id="ab97c-632">*MyNamedClient* 예제에서 해당 메시지는 로그 범주 `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`에 대해 기록됩니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-632">In the *MyNamedClient* example, those messages are logged against the log category `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`.</span></span> <span data-ttu-id="ab97c-633">요청의 경우 이는 요청이 네트워크에서 전송되기 직전 및 다른 모든 처리기가 실행된 후에 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-633">For the request, this occurs after all other handlers have run and immediately before the request is sent out on the network.</span></span> <span data-ttu-id="ab97c-634">응답 시 이 로깅은 처리기 파이프라인을 통해 응답이 다시 전달되기 전의 응답 상태를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-634">On the response, this logging includes the state of the response before it passes back through the handler pipeline.</span></span>

<span data-ttu-id="ab97c-635">파이프라인 외부 및 내부에서 로깅을 사용하도록 설정하면 다른 파이프라인 처리기가 수행한 변경 내용을 검사할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-635">Enabling logging outside and inside the pipeline enables inspection of the changes made by the other pipeline handlers.</span></span> <span data-ttu-id="ab97c-636">예를 들어 여기에는 요청 헤더 또는 응답 상태 코드에 대한 변경이 포함될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-636">This may include changes to request headers, for example, or to the response status code.</span></span>

<span data-ttu-id="ab97c-637">로그 범주에 클라이언트의 이름을 포함하면 필요한 경우 명명된 특정 클라이언트에 대한 로그 필터링을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-637">Including the name of the client in the log category enables log filtering for specific named clients where necessary.</span></span>

## <a name="configure-the-httpmessagehandler"></a><span data-ttu-id="ab97c-638">HttpMessageHandler 구성</span><span class="sxs-lookup"><span data-stu-id="ab97c-638">Configure the HttpMessageHandler</span></span>

<span data-ttu-id="ab97c-639">클라이언트가 사용하는 내부 `HttpMessageHandler`의 구성을 제어해야 할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-639">It may be necessary to control the configuration of the inner `HttpMessageHandler` used by a client.</span></span>

<span data-ttu-id="ab97c-640">`IHttpClientBuilder`는 명명된 또는 형식화된 클라이언트를 추가할 때 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-640">An `IHttpClientBuilder` is returned when adding named or typed clients.</span></span> <span data-ttu-id="ab97c-641"><xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> 확장 메서드는 대리자를 정의하는 데 사용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-641">The <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> extension method can be used to define a delegate.</span></span> <span data-ttu-id="ab97c-642">대리자는 해당 클라이언트가 사용하는 기본 `HttpMessageHandler`을 만들고 구성하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-642">The delegate is used to create and configure the primary `HttpMessageHandler` used by that client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet12)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a><span data-ttu-id="ab97c-643">콘솔 앱에서 IHttpClientFactory 사용</span><span class="sxs-lookup"><span data-stu-id="ab97c-643">Use IHttpClientFactory in a console app</span></span>

<span data-ttu-id="ab97c-644">콘솔 앱에서 프로젝트에 다음 패키지 참조를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-644">In a console app, add the following package references to the project:</span></span>

* [<span data-ttu-id="ab97c-645">Microsoft.Extensions.Hosting</span><span class="sxs-lookup"><span data-stu-id="ab97c-645">Microsoft.Extensions.Hosting</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [<span data-ttu-id="ab97c-646">Microsoft.Extensions.Http</span><span class="sxs-lookup"><span data-stu-id="ab97c-646">Microsoft.Extensions.Http</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Http)

<span data-ttu-id="ab97c-647">다음 예제에서는</span><span class="sxs-lookup"><span data-stu-id="ab97c-647">In the following example:</span></span>

* <span data-ttu-id="ab97c-648"><xref:System.Net.Http.IHttpClientFactory>는 [제너릭 호스트의](xref:fundamentals/host/generic-host) 서비스 컨테이너에 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-648"><xref:System.Net.Http.IHttpClientFactory> is registered in the [Generic Host's](xref:fundamentals/host/generic-host) service container.</span></span>
* <span data-ttu-id="ab97c-649">`MyService`에서는 `HttpClient`를 만드는 데 사용하는 서비스에서 클라이언트 팩터리 인스턴스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-649">`MyService` creates a client factory instance from the service, which is used to create an `HttpClient`.</span></span> <span data-ttu-id="ab97c-650">`HttpClient`는 웹 페이지를 검색하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-650">`HttpClient` is used to retrieve a webpage.</span></span>
* <span data-ttu-id="ab97c-651">`Main`을 통해서는 서비스의 `GetPage` 메서드를 실행하고 웹 페이지 콘텐츠의 처음 500자를 콘솔에 씁니다.</span><span class="sxs-lookup"><span data-stu-id="ab97c-651">`Main` creates a scope to execute the service's `GetPage` method and write the first 500 characters of the webpage content to the console.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="additional-resources"></a><span data-ttu-id="ab97c-652">추가 자료</span><span class="sxs-lookup"><span data-stu-id="ab97c-652">Additional resources</span></span>

* [<span data-ttu-id="ab97c-653">HttpClientFactory를 사용하여 복원력 있는 HTTP 요청 구현</span><span class="sxs-lookup"><span data-stu-id="ab97c-653">Use HttpClientFactory to implement resilient HTTP requests</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [<span data-ttu-id="ab97c-654">HttpClientFactory 및 Polly 정책을 통해 지수 백오프를 사용하여 HTTP 호출 다시 시도 구현</span><span class="sxs-lookup"><span data-stu-id="ab97c-654">Implement HTTP call retries with exponential backoff with HttpClientFactory and Polly policies</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [<span data-ttu-id="ab97c-655">회로 차단기 패턴 구현</span><span class="sxs-lookup"><span data-stu-id="ab97c-655">Implement the Circuit Breaker pattern</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)

::: moniker-end
