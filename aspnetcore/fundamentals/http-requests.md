---
title: ASP.NET Core에서 IHttpClientFactory를 사용하여 HTTP 요청 만들기
author: stevejgordon
description: IHttpClientFactory 인터페이스를 사용하여 ASP.NET Core에서 논리적 HttpClient 인스턴스를 관리하는 방법에 대해 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 02/09/2020
uid: fundamentals/http-requests
ms.openlocfilehash: 912be34ae0ee25837a94aab65443f15b17ab4556
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78648297"
---
# <a name="make-http-requests-using-ihttpclientfactory-in-aspnet-core"></a><span data-ttu-id="f9e53-103">ASP.NET Core에서 IHttpClientFactory를 사용하여 HTTP 요청 만들기</span><span class="sxs-lookup"><span data-stu-id="f9e53-103">Make HTTP requests using IHttpClientFactory in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f9e53-104">작성자: [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak),  [Steve Gordon](https://github.com/stevejgordon), [Rick Anderson](https://twitter.com/RickAndMSFT) 및 [Kirk Larkin](https://github.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="f9e53-104">By [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak),  [Steve Gordon](https://github.com/stevejgordon), [Rick Anderson](https://twitter.com/RickAndMSFT), and [Kirk Larkin](https://github.com/serpent5)</span></span>

<span data-ttu-id="f9e53-105">앱에서 <xref:System.Net.Http.IHttpClientFactory>를 등록하여 <xref:System.Net.Http.HttpClient> 인스턴스를 구성하고 만드는 데 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-105">An <xref:System.Net.Http.IHttpClientFactory> can be registered and used to configure and create <xref:System.Net.Http.HttpClient> instances in an app.</span></span> <span data-ttu-id="f9e53-106">`IHttpClientFactory`는 다음과 같은 이점을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-106">`IHttpClientFactory` offers the following benefits:</span></span>

* <span data-ttu-id="f9e53-107">논리적 `HttpClient` 인스턴스를 구성하고 이름을 지정하기 위한 중앙 위치를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-107">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="f9e53-108">예를 들어, *github*라는 클라이언트를 [GitHub](https://github.com/)에 액세스하도록 등록 및 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-108">For example, a client named  *github* could be registered and configured to access [GitHub](https://github.com/).</span></span> <span data-ttu-id="f9e53-109">일반적인 액세스를 위한 기본 클라이언트를 등록할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-109">A default client can be registered for general access.</span></span>
* <span data-ttu-id="f9e53-110">`HttpClient`에서 위임 처리기를 통해 나가는 미들웨어의 개념을 체계화합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-110">Codifies the concept of outgoing middleware via delegating handlers in `HttpClient`.</span></span> <span data-ttu-id="f9e53-111">Polly 기반 미들웨어에 대한 확장을 제공하여 `HttpClient`에서의 핸들러 위임을 활용합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-111">Provides extensions for Polly-based middleware to take advantage of delegating handlers in `HttpClient`.</span></span>
* <span data-ttu-id="f9e53-112">기본 `HttpClientMessageHandler` 인스턴스의 풀링 및 수명을 관리합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-112">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances.</span></span> <span data-ttu-id="f9e53-113">자동 관리가 `HttpClient` 수명을 수동으로 관리할 때 발생하는 일반적인 DNS(Domain Name System) 문제를 방지해 줍니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-113">Automatic management avoids common DNS (Domain Name System) problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="f9e53-114">팩터리에서 만든 클라이언트를 통해 전송된 모든 요청에 대해 구성 가능한 로깅 경험(`ILogger`을 통해)을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-114">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="f9e53-115">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([다운로드 방법](xref:index#how-to-download-a-sample)). 다운로드 예제는 영역을 테스트하기 위한 기초적인 앱을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-115">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="f9e53-116">이 항목 버전의 샘플 코드는 <xref:System.Text.Json>을 사용하여 HTTP 응답으로 반환된 JSON 콘텐츠를 역직렬화합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-116">The sample code in this topic version uses <xref:System.Text.Json> to deserialize JSON content returned in HTTP responses.</span></span> <span data-ttu-id="f9e53-117">`Json.NET` 및 `ReadAsAsync<T>`를 사용하는 샘플의 경우, 버전 선택기를 사용하여 이 항목의 2.x 버전을 선택하세요.</span><span class="sxs-lookup"><span data-stu-id="f9e53-117">For samples that use `Json.NET` and `ReadAsAsync<T>`, use the version selector to select a 2.x version of this topic.</span></span>

## <a name="consumption-patterns"></a><span data-ttu-id="f9e53-118">사용 패턴</span><span class="sxs-lookup"><span data-stu-id="f9e53-118">Consumption patterns</span></span>

<span data-ttu-id="f9e53-119">앱에서 `IHttpClientFactory`를 사용할 수 있는 몇 가지 방법이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-119">There are several ways `IHttpClientFactory` can be used in an app:</span></span>

* [<span data-ttu-id="f9e53-120">기본적인 사용 방법</span><span class="sxs-lookup"><span data-stu-id="f9e53-120">Basic usage</span></span>](#basic-usage)
* [<span data-ttu-id="f9e53-121">명명된 클라이언트</span><span class="sxs-lookup"><span data-stu-id="f9e53-121">Named clients</span></span>](#named-clients)
* [<span data-ttu-id="f9e53-122">형식화된 클라이언트</span><span class="sxs-lookup"><span data-stu-id="f9e53-122">Typed clients</span></span>](#typed-clients)
* [<span data-ttu-id="f9e53-123">생성된 클라이언트</span><span class="sxs-lookup"><span data-stu-id="f9e53-123">Generated clients</span></span>](#generated-clients)

<span data-ttu-id="f9e53-124">가장 좋은 방법은 앱의 요구 사항에 따라서 달라집니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-124">The best approach depends upon the app's requirements.</span></span>

### <a name="basic-usage"></a><span data-ttu-id="f9e53-125">기본적인 사용 방법</span><span class="sxs-lookup"><span data-stu-id="f9e53-125">Basic usage</span></span>

<span data-ttu-id="f9e53-126">`AddHttpClient`를 호출하여 `IHttpClientFactory`를 등록할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-126">`IHttpClientFactory` can be registered by calling `AddHttpClient`:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

<span data-ttu-id="f9e53-127">[종속성 주입(DI)](xref:fundamentals/dependency-injection)을 사용하여 `IHttpClientFactory`를 요청할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-127">An `IHttpClientFactory` can be requested using [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="f9e53-128">다음 코드는 `IHttpClientFactory`를 사용하여 `HttpClient` 인스턴스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-128">The following code uses `IHttpClientFactory` to create an `HttpClient` instance:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

<span data-ttu-id="f9e53-129">앞서 나온 예제에서와 같이 `IHttpClientFactory`를 사용하는 것은 기존 앱을 리팩터링하는 좋은 방법입니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-129">Using `IHttpClientFactory` like in the preceding example is a good way to refactor an existing app.</span></span> <span data-ttu-id="f9e53-130">`HttpClient`가 사용되는 방식에는 어떠한 영향도 없습니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-130">It has no impact on how `HttpClient` is used.</span></span> <span data-ttu-id="f9e53-131">기존 앱에서 `HttpClient` 인스턴스가 만들어지는 위치에서 해당 코드를 <xref:System.Net.Http.IHttpClientFactory.CreateClient*>에 대한 호출로 대체합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-131">In places where `HttpClient` instances are created in an existing app, replace those occurrences with calls to <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span></span>

### <a name="named-clients"></a><span data-ttu-id="f9e53-132">명명된 클라이언트</span><span class="sxs-lookup"><span data-stu-id="f9e53-132">Named clients</span></span>

<span data-ttu-id="f9e53-133">명명된 클라이언트는 다음과 같은 경우에 적합합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-133">Named clients are a good choice when:</span></span>

* <span data-ttu-id="f9e53-134">앱에서 `HttpClient`를 서로 다른 곳에서 여러 번 사용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-134">The app requires many distinct uses of `HttpClient`.</span></span>
* <span data-ttu-id="f9e53-135">여러 `HttpClient`가 서로 다른 구성을 갖습니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-135">Many `HttpClient`s have different configuration.</span></span>

<span data-ttu-id="f9e53-136">명명된 `HttpClient`에 대한 구성은 `Startup.ConfigureServices`에서 등록하는 동안 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-136">Configuration for a named `HttpClient` can be specified during registration in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

<span data-ttu-id="f9e53-137">위의 코드에서 클라이언트는 다음을 사용하여 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-137">In the preceding code the client is configured with:</span></span>

* <span data-ttu-id="f9e53-138">기본 주소 `https://api.github.com/`.</span><span class="sxs-lookup"><span data-stu-id="f9e53-138">The base address `https://api.github.com/`.</span></span>
* <span data-ttu-id="f9e53-139">GitHub API를 사용하는 데 필요한 헤더 2개.</span><span class="sxs-lookup"><span data-stu-id="f9e53-139">Two headers required to work with the GitHub API.</span></span>

#### <a name="createclient"></a><span data-ttu-id="f9e53-140">CreateClient</span><span class="sxs-lookup"><span data-stu-id="f9e53-140">CreateClient</span></span>

<span data-ttu-id="f9e53-141"><xref:System.Net.Http.IHttpClientFactory.CreateClient*>가 호출될 때마다</span><span class="sxs-lookup"><span data-stu-id="f9e53-141">Each time <xref:System.Net.Http.IHttpClientFactory.CreateClient*> is called:</span></span>

* <span data-ttu-id="f9e53-142">`HttpClient`의 새 인스턴스가 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-142">A new instance of `HttpClient` is created.</span></span>
* <span data-ttu-id="f9e53-143">구성 작업이 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-143">The configuration action is called.</span></span>

<span data-ttu-id="f9e53-144">명명된 클라이언트를 만들려면 `CreateClient`로 이름을 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-144">To create a named client, pass its name into `CreateClient`:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

<span data-ttu-id="f9e53-145">위의 코드에서는 요청이 호스트 이름을 지정할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-145">In the preceding code, the request doesn't need to specify a hostname.</span></span> <span data-ttu-id="f9e53-146">클라이언트에 대해 구성된 기본 주소가 사용되었므로 코드는 경로만 전달할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-146">The code can pass just the path, since the base address configured for the client is used.</span></span>

### <a name="typed-clients"></a><span data-ttu-id="f9e53-147">형식화된 클라이언트</span><span class="sxs-lookup"><span data-stu-id="f9e53-147">Typed clients</span></span>

<span data-ttu-id="f9e53-148">형식화된 클라이언트:</span><span class="sxs-lookup"><span data-stu-id="f9e53-148">Typed clients:</span></span>

* <span data-ttu-id="f9e53-149">문자열을 키로 사용할 필요가 없이 명명된 클라이언트와 동일한 기능을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-149">Provide the same capabilities as named clients without the need to use strings as keys.</span></span>
* <span data-ttu-id="f9e53-150">클라이언트를 사용할 때 IntelliSense 및 컴파일러 도움말을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-150">Provides IntelliSense and compiler help when consuming clients.</span></span>
* <span data-ttu-id="f9e53-151">특정 `HttpClient`을 구성하고 상호 작용하기 위해 단일 위치를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-151">Provide a single location to configure and interact with a particular `HttpClient`.</span></span> <span data-ttu-id="f9e53-152">예를 들어, 다음과 같은 경우에 단일 형식화된 클라이언트를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-152">For example, a single typed client might be used:</span></span>
  * <span data-ttu-id="f9e53-153">단일 백 엔드 엔드포인트에 대해.</span><span class="sxs-lookup"><span data-stu-id="f9e53-153">For a single backend endpoint.</span></span>
  * <span data-ttu-id="f9e53-154">엔드포인트를 처리하는 모든 로직을 캡슐화하기 위해.</span><span class="sxs-lookup"><span data-stu-id="f9e53-154">To encapsulate all logic dealing with the endpoint.</span></span>
* <span data-ttu-id="f9e53-155">DI로 작업하고 앱에서 필요할 경우 삽입할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-155">Work with DI and can be injected where required in the app.</span></span>

<span data-ttu-id="f9e53-156">형식화된 클라이언트는 생성자에서 `HttpClient` 매개 변수를 받습니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-156">A typed client accepts an `HttpClient` parameter in its constructor:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="f9e53-157">위의 코드에서</span><span class="sxs-lookup"><span data-stu-id="f9e53-157">In the preceding code:</span></span>

* <span data-ttu-id="f9e53-158">구성이 형식화된 클라이언트로 이동되었습니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-158">The configuration is moved into the typed client.</span></span>
* <span data-ttu-id="f9e53-159">`HttpClient` 개체는 공용 속성으로 노출됩니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-159">The `HttpClient` object is exposed as a public property.</span></span>

<span data-ttu-id="f9e53-160">`HttpClient` 기능을 노출하는 API 특정 메서드를 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-160">API-specific methods can be created that expose `HttpClient` functionality.</span></span> <span data-ttu-id="f9e53-161">예를 들어, `GetAspNetDocsIssues` 메서드는 열린 문제를 검색하는 코드를 캡슐화합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-161">For example, the `GetAspNetDocsIssues` method encapsulates code to retrieve open issues.</span></span>

<span data-ttu-id="f9e53-162">다음 코드는 `Startup.ConfigureServices`에서 <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*>를 호출하여 형식화된 클라이언트 클래스를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-162">The following code calls <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> in `Startup.ConfigureServices` to register a typed client class:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

<span data-ttu-id="f9e53-163">형식화된 클라이언트는 DI를 사용하여 일시적으로 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-163">The typed client is registered as transient with DI.</span></span> <span data-ttu-id="f9e53-164">위의 코드에서 `AddHttpClient`는 `GitHubService`를 임시 서비스로 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-164">In the preceding code, `AddHttpClient` registers `GitHubService` as a transient service.</span></span> <span data-ttu-id="f9e53-165">이 등록에서는 팩터리 메서드를 사용하여 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-165">This registration uses a factory method to:</span></span>

1. <span data-ttu-id="f9e53-166">`HttpClient`의 인스턴스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-166">Create an instance of `HttpClient`.</span></span>
1. <span data-ttu-id="f9e53-167">`HttpClient`의 인스턴스를 생성자에 전달하여 `GitHubService`의 인스턴스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-167">Create an instance of `GitHubService`, passing in the instance of `HttpClient` to its constructor.</span></span>

<span data-ttu-id="f9e53-168">형식화된 클라이언트는 직접 주입되고 사용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-168">The typed client can be injected and consumed directly:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

<span data-ttu-id="f9e53-169">형식화된 클라이언트의 생성자 대신 `Startup.ConfigureServices`에서 등록하는 동안 형식화된 클라이언트에 대한 구성을 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-169">The configuration for a typed client can be specified during registration in `Startup.ConfigureServices`, rather than in the typed client's constructor:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

<span data-ttu-id="f9e53-170">형식화된 클라이언트 내에서 `HttpClient`를 캡슐화할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-170">The `HttpClient` can be encapsulated within a typed client.</span></span> <span data-ttu-id="f9e53-171">속성으로 노출하는 대신 내부적으로 `HttpClient` 인스턴스를 호출하는 메서드를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-171">Rather than exposing it as a property, define a method which calls the `HttpClient` instance internally:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

<span data-ttu-id="f9e53-172">위의 코드에서는 `HttpClient`가 private 필드로 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-172">In the preceding code, the `HttpClient` is stored in a private field.</span></span> <span data-ttu-id="f9e53-173">`HttpClient`에 대한 액세스는 public `GetRepos` 메서드에 의해 이루어집니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-173">Access to the `HttpClient` is by the public `GetRepos` method.</span></span>

### <a name="generated-clients"></a><span data-ttu-id="f9e53-174">생성된 클라이언트</span><span class="sxs-lookup"><span data-stu-id="f9e53-174">Generated clients</span></span>

<span data-ttu-id="f9e53-175">`IHttpClientFactory`는 [Refit](https://github.com/paulcbetts/refit)과 같은 타사 라이브러리와 함께 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-175">`IHttpClientFactory` can be used in combination with third-party libraries such as [Refit](https://github.com/paulcbetts/refit).</span></span> <span data-ttu-id="f9e53-176">Refit은 .NET용 REST 라이브러리입니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-176">Refit is a REST library for .NET.</span></span> <span data-ttu-id="f9e53-177">REST API를 라이브 인터페이스로 변환합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-177">It converts REST APIs into live interfaces.</span></span> <span data-ttu-id="f9e53-178">인터페이스의 구현은 `HttpClient`를 사용하여 외부 HTTP를 호출하도록 `RestService`에 의해 동적으로 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-178">An implementation of the interface is generated dynamically by the `RestService`, using `HttpClient` to make the external HTTP calls.</span></span>

<span data-ttu-id="f9e53-179">외부 API와 해당 응답을 나타내기 위한 인터페이스와 회신이 정의됩니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-179">An interface and a reply are defined to represent the external API and its response:</span></span>

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

<span data-ttu-id="f9e53-180">구현을 생성하기 위해 Refit를 사용하여 형식화된 클라이언트를 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-180">A typed client can be added, using Refit to generate the implementation:</span></span>

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

<span data-ttu-id="f9e53-181">DI 및 Refit에서 제공한 구현을 통해 필요한 곳에서 정의된 인터페이스를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-181">The defined interface can be consumed where necessary, with the implementation provided by DI and Refit:</span></span>

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

## <a name="outgoing-request-middleware"></a><span data-ttu-id="f9e53-182">나가는 요청 미들웨어</span><span class="sxs-lookup"><span data-stu-id="f9e53-182">Outgoing request middleware</span></span>

<span data-ttu-id="f9e53-183">`HttpClient`에는 나가는 HTTP 요청을 위해 함께 연결될 수 있는 위임 처리기라는 개념이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-183">`HttpClient` has the concept of delegating handlers that can be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="f9e53-184">`IHttpClientFactory`:</span><span class="sxs-lookup"><span data-stu-id="f9e53-184">`IHttpClientFactory`:</span></span>

* <span data-ttu-id="f9e53-185">각 명명된 클라이언트에 적용할 처리기를 쉽게 정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-185">Simplifies defining the handlers to apply for each named client.</span></span>
* <span data-ttu-id="f9e53-186">나가는 요청 미들웨어 파이프라인을 만들기 위한 여러 처리기의 등록 및 연결을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-186">Supports registration and chaining of multiple handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="f9e53-187">이러한 처리기 각각은 나가는 요청 전후에 작업을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-187">Each of these handlers is able to perform work before and after the outgoing request.</span></span> <span data-ttu-id="f9e53-188">이 패턴은</span><span class="sxs-lookup"><span data-stu-id="f9e53-188">This pattern:</span></span>

  * <span data-ttu-id="f9e53-189">ASP.NET Core의 인바운드 미들웨어 파이프라인과 비슷합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-189">Is similar to the inbound middleware pipeline in ASP.NET Core.</span></span>
  * <span data-ttu-id="f9e53-190">다음과 같은 HTTP 요청을 둘러싼 횡단 관심사를 관리하기 위한 메커니즘을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-190">Provides a mechanism to manage cross-cutting concerns around HTTP requests, such as:</span></span>

    * <span data-ttu-id="f9e53-191">캐싱</span><span class="sxs-lookup"><span data-stu-id="f9e53-191">caching</span></span>
    * <span data-ttu-id="f9e53-192">오류 처리</span><span class="sxs-lookup"><span data-stu-id="f9e53-192">error handling</span></span>
    * <span data-ttu-id="f9e53-193">직렬화</span><span class="sxs-lookup"><span data-stu-id="f9e53-193">serialization</span></span>
    * <span data-ttu-id="f9e53-194">로깅</span><span class="sxs-lookup"><span data-stu-id="f9e53-194">logging</span></span>

<span data-ttu-id="f9e53-195">위임 처리기를 만들려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-195">To create a delegating handler:</span></span>

* <span data-ttu-id="f9e53-196"><xref:System.Net.Http.DelegatingHandler>를 파생시킵니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-196">Derive from <xref:System.Net.Http.DelegatingHandler>.</span></span>
* <span data-ttu-id="f9e53-197"><xref:System.Net.Http.DelegatingHandler.SendAsync*>을 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-197">Override <xref:System.Net.Http.DelegatingHandler.SendAsync*>.</span></span> <span data-ttu-id="f9e53-198">파이프라인의 다음 처리기로 요청을 전달하기 전에 코드를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-198">Execute code before passing the request to the next handler in the pipeline:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

<span data-ttu-id="f9e53-199">위의 코드는 `X-API-KEY` 헤더가 요청에 있는지 여부를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-199">The preceding code checks if the `X-API-KEY` header is in the request.</span></span> <span data-ttu-id="f9e53-200">`X-API-KEY`가 누락된 경우 <xref:System.Net.HttpStatusCode.BadRequest>가 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-200">If `X-API-KEY` is missing, <xref:System.Net.HttpStatusCode.BadRequest> is returned.</span></span>

<span data-ttu-id="f9e53-201"><xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*?displayProperty=fullName>가 있는 `HttpClient`의 구성에는 둘 이상의 핸들러가 추가될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-201">More than one handler can be added to the configuration for an `HttpClient` with <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*?displayProperty=fullName>:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup2.cs?name=snippet1)]

<span data-ttu-id="f9e53-202">위의 코드에서 `ValidateHeaderHandler`는 DI에 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-202">In the preceding code, the `ValidateHeaderHandler` is registered with DI.</span></span> <span data-ttu-id="f9e53-203">`IHttpClientFactory`는 각 처리기에 대해 별도의 DI 범위를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-203">The `IHttpClientFactory` creates a separate DI scope for each handler.</span></span> <span data-ttu-id="f9e53-204">처리기는 모든 범위의 서비스에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-204">Handlers can depend upon services of any scope.</span></span> <span data-ttu-id="f9e53-205">처리기가 삭제되면 처리기가 사용하는 서비스가 삭제됩니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-205">Services that handlers depend upon are disposed when the handler is disposed.</span></span>

<span data-ttu-id="f9e53-206">일단 등록되면 처리기의 형식을 전달하여 <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*>를 호출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-206">Once registered, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> can be called, passing in the type for the handler.</span></span>

<span data-ttu-id="f9e53-207">여러 처리기를 실행해야 하는 순서에 따라 등록할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-207">Multiple handlers can be registered in the order that they should execute.</span></span> <span data-ttu-id="f9e53-208">각 처리기는 최종 `HttpClientHandler`가 요청을 실행할 때까지 다음 처리기를 래핑합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-208">Each handler wraps the next handler until the final `HttpClientHandler` executes the request:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

<span data-ttu-id="f9e53-209">다음 방법 중 하나를 사용하여 메시지 처리기와 요청별 상태를 공유하세요.</span><span class="sxs-lookup"><span data-stu-id="f9e53-209">Use one of the following approaches to share per-request state with message handlers:</span></span>

* <span data-ttu-id="f9e53-210">[HttpRequestMessage.Properties](xref:System.Net.Http.HttpRequestMessage.Properties)를 사용하여 데이터를 처리기로 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-210">Pass data into the handler using [HttpRequestMessage.Properties](xref:System.Net.Http.HttpRequestMessage.Properties).</span></span>
* <span data-ttu-id="f9e53-211"><xref:Microsoft.AspNetCore.Http.IHttpContextAccessor>를 사용하여 현재 요청에 액세스합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-211">Use <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> to access the current request.</span></span>
* <span data-ttu-id="f9e53-212">사용자 지정 <xref:System.Threading.AsyncLocal`1> 스토리지 개체를 만들어 데이터를 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-212">Create a custom <xref:System.Threading.AsyncLocal`1> storage object to pass the data.</span></span>

## <a name="use-polly-based-handlers"></a><span data-ttu-id="f9e53-213">Polly 기반 처리기 사용</span><span class="sxs-lookup"><span data-stu-id="f9e53-213">Use Polly-based handlers</span></span>

<span data-ttu-id="f9e53-214">`IHttpClientFactory`는 타사 라이브러리 [Polly](https://github.com/App-vNext/Polly)와 통합됩니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-214">`IHttpClientFactory` integrates with the third-party library [Polly](https://github.com/App-vNext/Polly).</span></span> <span data-ttu-id="f9e53-215">Polly는 .NET용 포괄적인 회복탄력성 및 일시적 오류 처리 라이브러리입니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-215">Polly is a comprehensive resilience and transient fault-handling library for .NET.</span></span> <span data-ttu-id="f9e53-216">개발자는 이를 사용하여 재시도, 회로 차단기, 시간 초과, 격벽 격리 및 대체(Fallback) 같은 정책을 유연하고 스레드로부터 안전한 방식으로 표현할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-216">It allows developers to express policies such as Retry, Circuit Breaker, Timeout, Bulkhead Isolation, and Fallback in a fluent and thread-safe manner.</span></span>

<span data-ttu-id="f9e53-217">구성된 `HttpClient` 인스턴스를 통해 Polly 정책을 사용할 수 있도록 확장 메서드가 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-217">Extension methods are provided to enable the use of Polly policies with configured `HttpClient` instances.</span></span> <span data-ttu-id="f9e53-218">Polly 확장은 클라이언트에 Polly 기반 처리기를 추가하는 것을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-218">The Polly extensions support adding Polly-based handlers to clients.</span></span> <span data-ttu-id="f9e53-219">Polly를 사용하려면 [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet 패키지가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-219">Polly requires the [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet package.</span></span>

### <a name="handle-transient-faults"></a><span data-ttu-id="f9e53-220">일시적인 오류 처리</span><span class="sxs-lookup"><span data-stu-id="f9e53-220">Handle transient faults</span></span>

<span data-ttu-id="f9e53-221">오류는 외부 HTTP 호출이 일시적인 경우 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-221">Faults typically occur when external HTTP calls are transient.</span></span> <span data-ttu-id="f9e53-222"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*>를 사용하면 정책에서 일시적인 오류를 처리하도록 정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-222"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> allows a policy to be defined to handle transient errors.</span></span> <span data-ttu-id="f9e53-223">`AddTransientHttpErrorPolicy`를 사용하여 구성한 정책은 다음과 같은 응답을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-223">Policies configured with `AddTransientHttpErrorPolicy` handle the following responses:</span></span>

* <xref:System.Net.Http.HttpRequestException>
* <span data-ttu-id="f9e53-224">HTTP 5xx</span><span class="sxs-lookup"><span data-stu-id="f9e53-224">HTTP 5xx</span></span>
* <span data-ttu-id="f9e53-225">HTTP 408</span><span class="sxs-lookup"><span data-stu-id="f9e53-225">HTTP 408</span></span>

<span data-ttu-id="f9e53-226">`AddTransientHttpErrorPolicy`는 가능한 일시적 오류를 나타내는 오류를 처리하기 위해 구성된 `PolicyBuilder` 개체에 대한 액세스를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-226">`AddTransientHttpErrorPolicy` provides access to a `PolicyBuilder` object configured to handle errors representing a possible transient fault:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup3.cs?name=snippet1)]

<span data-ttu-id="f9e53-227">위의 코드에서는 `WaitAndRetryAsync` 정책을 정의하고 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-227">In the preceding code, a `WaitAndRetryAsync` policy is defined.</span></span> <span data-ttu-id="f9e53-228">실패한 요청은 최대 세 번까지 다시 시도되며 시도 간 600밀리초의 지연 간격을 둡니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-228">Failed requests are retried up to three times with a delay of 600 ms between attempts.</span></span>

### <a name="dynamically-select-policies"></a><span data-ttu-id="f9e53-229">동적으로 정책 선택</span><span class="sxs-lookup"><span data-stu-id="f9e53-229">Dynamically select policies</span></span>

<span data-ttu-id="f9e53-230">Polly 기반 처리기를 추가하는 데 사용할 수 있는 확장 메서드(예: <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandler*>)가 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-230">Extension methods are provided to add Polly-based handlers, for example, <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandler*>.</span></span> <span data-ttu-id="f9e53-231">다음 `AddPolicyHandler` 오버로드는 요청을 검사하여 어느 정책을 적용할지 결정합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-231">The following `AddPolicyHandler` overload inspects the request to decide which policy to apply:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

<span data-ttu-id="f9e53-232">위의 코드에서는 나가는 요청이 HTTP GET인 경우 10초 시간 제한이 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-232">In the preceding code, if the outgoing request is an HTTP GET, a 10-second timeout is applied.</span></span> <span data-ttu-id="f9e53-233">다른 HTTP 메서드의 경우 30초 시간 제한이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-233">For any other HTTP method, a 30-second timeout is used.</span></span>

### <a name="add-multiple-polly-handlers"></a><span data-ttu-id="f9e53-234">여러 Polly 처리기 추가</span><span class="sxs-lookup"><span data-stu-id="f9e53-234">Add multiple Polly handlers</span></span>

<span data-ttu-id="f9e53-235">Polly 정책을 중첩하는 것은 일반적입니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-235">It's common to nest Polly policies:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

<span data-ttu-id="f9e53-236">앞의 예제에서:</span><span class="sxs-lookup"><span data-stu-id="f9e53-236">In the preceding example:</span></span>

* <span data-ttu-id="f9e53-237">두 개의 처리기가 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-237">Two handlers are added.</span></span>
* <span data-ttu-id="f9e53-238">첫 번째 처리기는 재시도 정책을 추가하기 위해 <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*>를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-238">The first handler uses <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> to add a retry policy.</span></span> <span data-ttu-id="f9e53-239">실패한 요청은 최대 세 번까지 다시 시도됩니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-239">Failed requests are retried up to three times.</span></span>
* <span data-ttu-id="f9e53-240">두 번째 호출 `AddTransientHttpErrorPolicy`는 회로 차단기 정책을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-240">The second `AddTransientHttpErrorPolicy` call adds a circuit breaker policy.</span></span> <span data-ttu-id="f9e53-241">5번의 시도가 순차적으로 실패하는 경우 추가적인 외부 요청은 30초 동안 차단됩니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-241">Further external requests are blocked for 30 seconds if 5 failed attempts occur sequentially.</span></span> <span data-ttu-id="f9e53-242">회로 차단기 정책은 상태를 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-242">Circuit breaker policies are stateful.</span></span> <span data-ttu-id="f9e53-243">이 클라이언트를 통한 모든 호출은 동일한 회로 상태를 공유합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-243">All calls through this client share the same circuit state.</span></span>

### <a name="add-policies-from-the-polly-registry"></a><span data-ttu-id="f9e53-244">Polly 레지스트리로부터 정책 추가</span><span class="sxs-lookup"><span data-stu-id="f9e53-244">Add policies from the Polly registry</span></span>

<span data-ttu-id="f9e53-245">정기적으로 사용되는 정책을 관리하는 방법은 정책을 한 번 정의하고 이를 `PolicyRegistry`에 등록하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-245">An approach to managing regularly used policies is to define them once and register them with a `PolicyRegistry`.</span></span>

<span data-ttu-id="f9e53-246">다음 코드에서:</span><span class="sxs-lookup"><span data-stu-id="f9e53-246">In the following code:</span></span>

* <span data-ttu-id="f9e53-247">“regular” 정책과 “long” 정책이 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-247">The "regular" and "long" polices are added.</span></span>
* <span data-ttu-id="f9e53-248"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandlerFromRegistry*>가 레지스트리로부터 “regular” 정책과 “long” 정책을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-248"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandlerFromRegistry*>  adds the "regular" and "long" policies from the registry.</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup4.cs?name=snippet1)]

<span data-ttu-id="f9e53-249">`IHttpClientFactory` 및 Polly 통합에 대한 자세한 내용은 [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f9e53-249">For more information on `IHttpClientFactory` and Polly integrations, see the [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span></span>

## <a name="httpclient-and-lifetime-management"></a><span data-ttu-id="f9e53-250">HttpClient 및 수명 관리</span><span class="sxs-lookup"><span data-stu-id="f9e53-250">HttpClient and lifetime management</span></span>

<span data-ttu-id="f9e53-251">`IHttpClientFactory`에서 `CreateClient`가 호출될 때마다 새로운 `HttpClient` 인스턴스가 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-251">A new `HttpClient` instance is returned each time `CreateClient` is called on the `IHttpClientFactory`.</span></span> <span data-ttu-id="f9e53-252">명명된 클라이언트마다 <xref:System.Net.Http.HttpMessageHandler>가 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-252">An <xref:System.Net.Http.HttpMessageHandler> is created per named client.</span></span> <span data-ttu-id="f9e53-253">팩터리는 `HttpMessageHandler` 인스턴스의 수명을 관리합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-253">The factory manages the lifetimes of the `HttpMessageHandler` instances.</span></span>

<span data-ttu-id="f9e53-254">`IHttpClientFactory`는 리소스 사용을 줄이기 위해 팩터리에서 만든 `HttpMessageHandler` 인스턴스를 풀링합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-254">`IHttpClientFactory` pools the `HttpMessageHandler` instances created by the factory to reduce resource consumption.</span></span> <span data-ttu-id="f9e53-255">수명이 만료되지 않은 경우, 새 `HttpClient` 인스턴스를 만들 때 풀에서 `HttpMessageHandler` 인스턴스가 재사용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-255">An `HttpMessageHandler` instance may be reused from the pool when creating a new `HttpClient` instance if its lifetime hasn't expired.</span></span>

<span data-ttu-id="f9e53-256">일반적으로 각 처리기는 자체적인 기본 HTTP 연결을 관리하므로 처리기의 풀링이 적합합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-256">Pooling of handlers is desirable as each handler typically manages its own underlying HTTP connections.</span></span> <span data-ttu-id="f9e53-257">필요한 것보다 많은 처리기를 만들면 연결 지연이 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-257">Creating more handlers than necessary can result in connection delays.</span></span> <span data-ttu-id="f9e53-258">또한 일부 처리기는 무한정으로 연결을 열어 놓아 처리기가 DNS(Domain Name System) 변경에 대응하는 것을 막을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-258">Some handlers also keep connections open indefinitely, which can prevent the handler from reacting to DNS (Domain Name System) changes.</span></span>

<span data-ttu-id="f9e53-259">기본 처리기 수명은 2분입니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-259">The default handler lifetime is two minutes.</span></span> <span data-ttu-id="f9e53-260">명명된 클라이언트별 기준으로 기본값을 재정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-260">The default value can be overridden on a per named client basis:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup5.cs?name=snippet1)]

<span data-ttu-id="f9e53-261">`HttpClient` 인스턴스는 일반적으로 삭제가 필요하지 **않은** .NET 개체로 간주할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-261">`HttpClient` instances can generally be treated as .NET objects **not** requiring disposal.</span></span> <span data-ttu-id="f9e53-262">삭제는 나가는 요청을 취소하고 <xref:System.IDisposable.Dispose*>를 호출한 후에는 지정된 `HttpClient` 인스턴스가 사용될 수 없도록 보장합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-262">Disposal cancels outgoing requests and guarantees the given `HttpClient` instance can't be used after calling <xref:System.IDisposable.Dispose*>.</span></span> <span data-ttu-id="f9e53-263">`IHttpClientFactory`는 `HttpClient` 인스턴스에서 사용되는 리소스를 추적하고 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-263">`IHttpClientFactory` tracks and disposes resources used by `HttpClient` instances.</span></span>

<span data-ttu-id="f9e53-264">장기간 단일 `HttpClient` 인스턴스를 활성 상태로 유지하는 것은 `IHttpClientFactory`가 등장하기 전에 사용되던 일반적인 패턴입니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-264">Keeping a single `HttpClient` instance alive for a long duration is a common pattern used before the inception of `IHttpClientFactory`.</span></span> <span data-ttu-id="f9e53-265">이 패턴은 `IHttpClientFactory`로 마이그레이션한 이후에는 불필요합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-265">This pattern becomes unnecessary after migrating to `IHttpClientFactory`.</span></span>

### <a name="alternatives-to-ihttpclientfactory"></a><span data-ttu-id="f9e53-266">IHttpClientFactory의 대안</span><span class="sxs-lookup"><span data-stu-id="f9e53-266">Alternatives to IHttpClientFactory</span></span>

<span data-ttu-id="f9e53-267">DI 지원 앱에서 `IHttpClientFactory`을(를) 사용하면 다음이 방지됩니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-267">Using `IHttpClientFactory` in a DI-enabled app avoids:</span></span>

* <span data-ttu-id="f9e53-268">`HttpMessageHandler` 인스턴스를 풀링하여 리소스 소모 문제가 발생했습니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-268">Resource exhaustion problems by pooling `HttpMessageHandler` instances.</span></span>
* <span data-ttu-id="f9e53-269">정기적으로 `HttpMessageHandler` 인스턴스를 순환하여 오래된 DNS 문제가 발생했습니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-269">Stale DNS problems by cycling `HttpMessageHandler` instances at regular intervals.</span></span>

<span data-ttu-id="f9e53-270">수명이 긴 <xref:System.Net.Http.SocketsHttpHandler> 인스턴스를 사용하여 위의 문제를 해결하는 다른 방법이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-270">There are alternative ways to solve the preceding problems using a long-lived <xref:System.Net.Http.SocketsHttpHandler> instance.</span></span>

- <span data-ttu-id="f9e53-271">앱 시작 시 `SocketsHttpHandler`의 인스턴스를 만들고 앱 수명 동안 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-271">Create an instance of `SocketsHttpHandler` when the app starts and use it for the life of the app.</span></span>
- <span data-ttu-id="f9e53-272">DNS 새로 고침 시간에 따라 적절한 값으로 <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime>을(를) 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-272">Configure <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> to an appropriate value based on DNS refresh times.</span></span>
- <span data-ttu-id="f9e53-273">필요에 따라 `new HttpClient(handler, disposeHandler: false)`을(를) 사용하여 `HttpClient` 인스턴스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-273">Create `HttpClient` instances using `new HttpClient(handler, disposeHandler: false)` as needed.</span></span>

<span data-ttu-id="f9e53-274">위의 방법은 비슷한 방식으로 `IHttpClientFactory`에서 해결하는 리소스 관리 문제를 해결합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-274">The preceding approaches solve the resource management problems that `IHttpClientFactory` solves in a similar way.</span></span>

- <span data-ttu-id="f9e53-275">`SocketsHttpHandler`은(는) `HttpClient` 인스턴스 간에 연결을 공유합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-275">The `SocketsHttpHandler` shares connections across `HttpClient` instances.</span></span> <span data-ttu-id="f9e53-276">이와 같이 공유하면 소켓이 소모되지 않도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-276">This sharing prevents socket exhaustion.</span></span>
- <span data-ttu-id="f9e53-277">오래된 DNS 문제를 방지하기 위해 `SocketsHttpHandler`에서 `PooledConnectionLifetime`에 따라 연결을 순환합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-277">The `SocketsHttpHandler` cycles connections according to `PooledConnectionLifetime` to avoid stale DNS problems.</span></span>

### <a name="cookies"></a><span data-ttu-id="f9e53-278">쿠키</span><span class="sxs-lookup"><span data-stu-id="f9e53-278">Cookies</span></span>

<span data-ttu-id="f9e53-279">풀링된 `HttpMessageHandler` 인스턴스는 `CookieContainer` 개체를 공유합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-279">The pooled `HttpMessageHandler` instances results in `CookieContainer` objects being shared.</span></span> <span data-ttu-id="f9e53-280">예상치 못한 `CookieContainer` 개체 공유로 잘못된 코드가 발생하는 경우가 많습니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-280">Unanticipated `CookieContainer` object sharing often results in incorrect code.</span></span> <span data-ttu-id="f9e53-281">쿠키가 필요한 앱의 경우 다음 중 하나를 고려하세요.</span><span class="sxs-lookup"><span data-stu-id="f9e53-281">For apps that require cookies, consider either:</span></span>

 - <span data-ttu-id="f9e53-282">자동 쿠키 처리 사용 안 함</span><span class="sxs-lookup"><span data-stu-id="f9e53-282">Disabling automatic cookie handling</span></span>
 - <span data-ttu-id="f9e53-283">`IHttpClientFactory` 방지</span><span class="sxs-lookup"><span data-stu-id="f9e53-283">Avoiding `IHttpClientFactory`</span></span>

<span data-ttu-id="f9e53-284"><xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*>을(를) 호출하여 자동 쿠키 처리를 사용하지 않도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-284">Call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> to disable automatic cookie handling:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a><span data-ttu-id="f9e53-285">로깅</span><span class="sxs-lookup"><span data-stu-id="f9e53-285">Logging</span></span>

<span data-ttu-id="f9e53-286">`IHttpClientFactory`을 통해 만든 클라이언트는 모든 요청에 대한 로그 메시지를 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-286">Clients created via `IHttpClientFactory` record log messages for all requests.</span></span> <span data-ttu-id="f9e53-287">기본 로그 메시지를 보려면 로깅 구성에서 적절한 정보 수준을 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-287">Enable the appropriate information level in the logging configuration to see the default log messages.</span></span> <span data-ttu-id="f9e53-288">요청 헤더의 로깅 등과 같은 추가 로깅은 추적 수준에서만 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-288">Additional logging, such as the logging of request headers, is only included at trace level.</span></span>

<span data-ttu-id="f9e53-289">각 클라이언트에 사용되는 로그 범주는 클라이언트의 이름을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-289">The log category used for each client includes the name of the client.</span></span> <span data-ttu-id="f9e53-290">예를 들어, *MyNamedClient*라는 클라이언트는 “System.Net.Http.HttpClient.**MyNamedClient**.LogicalHandler”의 범주를 사용하여 메시지를 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-290">A client named *MyNamedClient*, for example, logs messages with a category of "System.Net.Http.HttpClient.**MyNamedClient**.LogicalHandler".</span></span> <span data-ttu-id="f9e53-291">*LogicalHandler*라는 접미사가 붙은 메시지는 요청 처리기 파이프라인 외부에서 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-291">Messages suffixed with *LogicalHandler* occur outside the request handler pipeline.</span></span> <span data-ttu-id="f9e53-292">요청 시 파이프라인의 다른 모든 처리기에서 이를 처리하기 전에 메시지가 기록됩니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-292">On the request, messages are logged before any other handlers in the pipeline have processed it.</span></span> <span data-ttu-id="f9e53-293">응답 시 다른 모든 파이프라인 처리기가 응답을 받은 후에 메시지가 기록됩니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-293">On the response, messages are logged after any other pipeline handlers have received the response.</span></span>

<span data-ttu-id="f9e53-294">로깅은 요청 처리기 파이프라인 내부에서도 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-294">Logging also occurs inside the request handler pipeline.</span></span> <span data-ttu-id="f9e53-295">*MyNamedClient* 예제에서 해당 메시지는 로그 범주 “System.Net.Http.HttpClient.**MyNamedClient**.ClientHandler”에 대해 기록됩니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-295">In the *MyNamedClient* example, those messages are logged with the log category "System.Net.Http.HttpClient.**MyNamedClient**.ClientHandler".</span></span> <span data-ttu-id="f9e53-296">요청의 경우 이는 요청이 전송되기 직전 및 다른 모든 처리기가 실행된 후에 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-296">For the request, this occurs after all other handlers have run and immediately before the request is sent.</span></span> <span data-ttu-id="f9e53-297">응답 시 이 로깅은 처리기 파이프라인을 통해 응답이 다시 전달되기 전의 응답 상태를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-297">On the response, this logging includes the state of the response before it passes back through the handler pipeline.</span></span>

<span data-ttu-id="f9e53-298">파이프라인 외부 및 내부에서 로깅을 사용하도록 설정하면 다른 파이프라인 처리기가 수행한 변경 내용을 검사할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-298">Enabling logging outside and inside the pipeline enables inspection of the changes made by the other pipeline handlers.</span></span> <span data-ttu-id="f9e53-299">여기에는 요청 헤더 또는 응답 상태 코드에 대한 변경이 포함될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-299">This may include changes to request headers or to the response status code.</span></span>

<span data-ttu-id="f9e53-300">로그 범주에 클라이언트의 이름을 포함하면 명명된 특정 클라이언트에 대한 로그 필터링을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-300">Including the name of the client in the log category enables log filtering for specific named clients.</span></span>

## <a name="configure-the-httpmessagehandler"></a><span data-ttu-id="f9e53-301">HttpMessageHandler 구성</span><span class="sxs-lookup"><span data-stu-id="f9e53-301">Configure the HttpMessageHandler</span></span>

<span data-ttu-id="f9e53-302">클라이언트가 사용하는 내부 `HttpMessageHandler`의 구성을 제어해야 할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-302">It may be necessary to control the configuration of the inner `HttpMessageHandler` used by a client.</span></span>

<span data-ttu-id="f9e53-303">`IHttpClientBuilder`는 명명된 또는 형식화된 클라이언트를 추가할 때 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-303">An `IHttpClientBuilder` is returned when adding named or typed clients.</span></span> <span data-ttu-id="f9e53-304"><xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> 확장 메서드는 대리자를 정의하는 데 사용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-304">The <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> extension method can be used to define a delegate.</span></span> <span data-ttu-id="f9e53-305">대리자는 해당 클라이언트가 사용하는 기본 `HttpMessageHandler`을 만들고 구성하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-305">The delegate is used to create and configure the primary `HttpMessageHandler` used by that client:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup6.cs?name=snippet1)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a><span data-ttu-id="f9e53-306">콘솔 앱에서 IHttpClientFactory 사용</span><span class="sxs-lookup"><span data-stu-id="f9e53-306">Use IHttpClientFactory in a console app</span></span>

<span data-ttu-id="f9e53-307">콘솔 앱에서 프로젝트에 다음 패키지 참조를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-307">In a console app, add the following package references to the project:</span></span>

* [<span data-ttu-id="f9e53-308">Microsoft.Extensions.Hosting</span><span class="sxs-lookup"><span data-stu-id="f9e53-308">Microsoft.Extensions.Hosting</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [<span data-ttu-id="f9e53-309">Microsoft.Extensions.Http</span><span class="sxs-lookup"><span data-stu-id="f9e53-309">Microsoft.Extensions.Http</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Http)

<span data-ttu-id="f9e53-310">다음 예제에서는</span><span class="sxs-lookup"><span data-stu-id="f9e53-310">In the following example:</span></span>

* <span data-ttu-id="f9e53-311"><xref:System.Net.Http.IHttpClientFactory>는 [제너릭 호스트의](xref:fundamentals/host/generic-host) 서비스 컨테이너에 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-311"><xref:System.Net.Http.IHttpClientFactory> is registered in the [Generic Host's](xref:fundamentals/host/generic-host) service container.</span></span>
* <span data-ttu-id="f9e53-312">`MyService`에서는 `HttpClient`를 만드는 데 사용하는 서비스에서 클라이언트 팩터리 인스턴스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-312">`MyService` creates a client factory instance from the service, which is used to create an `HttpClient`.</span></span> <span data-ttu-id="f9e53-313">`HttpClient`는 웹 페이지를 검색하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-313">`HttpClient` is used to retrieve a webpage.</span></span>
* <span data-ttu-id="f9e53-314">`Main`을 통해서는 서비스의 `GetPage` 메서드를 실행하고 웹 페이지 콘텐츠의 처음 500자를 콘솔에 씁니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-314">`Main` creates a scope to execute the service's `GetPage` method and write the first 500 characters of the webpage content to the console.</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="header-propagation-middleware"></a><span data-ttu-id="f9e53-315">헤더 전파 미들웨어</span><span class="sxs-lookup"><span data-stu-id="f9e53-315">Header propagation middleware</span></span>

<span data-ttu-id="f9e53-316">헤더 전파는 들어오는 요청에서 나가는 HTTP 클라이언트 요청으로 HTTP 헤더를 전파하는 ASP.NET Core 미들웨어입니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-316">Header propagation is an ASP.NET Core middleware to propagate HTTP headers from the incoming request to the outgoing HTTP Client requests.</span></span> <span data-ttu-id="f9e53-317">헤더 전파를 사용하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-317">To use header propagation:</span></span>

* <span data-ttu-id="f9e53-318">[Microsoft.AspNetCore.HeaderPropagation](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation) 패키지를 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-318">Reference the [Microsoft.AspNetCore.HeaderPropagation](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation) package.</span></span>
* <span data-ttu-id="f9e53-319">`Startup`에서 미들웨어 및 `HttpClient`를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-319">Configure the middleware and `HttpClient` in `Startup`:</span></span>

  [!code-csharp[](http-requests/samples/3.x/Startup.cs?highlight=5-9,21&name=snippet)]

* <span data-ttu-id="f9e53-320">클라이언트는 아웃바운드 요청에 구성된 헤더를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-320">The client includes the configured headers on outbound requests:</span></span>

  ```csharp
  var client = clientFactory.CreateClient("MyForwardingClient");
  var response = client.GetAsync(...);
  ```

## <a name="additional-resources"></a><span data-ttu-id="f9e53-321">추가 자료</span><span class="sxs-lookup"><span data-stu-id="f9e53-321">Additional resources</span></span>

* [<span data-ttu-id="f9e53-322">HttpClientFactory를 사용하여 복원력 있는 HTTP 요청 구현</span><span class="sxs-lookup"><span data-stu-id="f9e53-322">Use HttpClientFactory to implement resilient HTTP requests</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [<span data-ttu-id="f9e53-323">HttpClientFactory 및 Polly 정책을 통해 지수 백오프를 사용하여 HTTP 호출 다시 시도 구현</span><span class="sxs-lookup"><span data-stu-id="f9e53-323">Implement HTTP call retries with exponential backoff with HttpClientFactory and Polly policies</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [<span data-ttu-id="f9e53-324">회로 차단기 패턴 구현</span><span class="sxs-lookup"><span data-stu-id="f9e53-324">Implement the Circuit Breaker pattern</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)
* [<span data-ttu-id="f9e53-325">.NET에서 JSON을 직렬화 및 역직렬화하는 방법</span><span class="sxs-lookup"><span data-stu-id="f9e53-325">How to serialize and deserialize JSON in .NET</span></span>](/dotnet/standard/serialization/system-text-json-how-to)

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="f9e53-326">작성자: [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak) 및 [Steve Gordon](https://github.com/stevejgordon)</span><span class="sxs-lookup"><span data-stu-id="f9e53-326">By [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak), and [Steve Gordon](https://github.com/stevejgordon)</span></span>

<span data-ttu-id="f9e53-327">앱에서 <xref:System.Net.Http.IHttpClientFactory>를 등록하여 <xref:System.Net.Http.HttpClient> 인스턴스를 구성하고 만드는 데 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-327">An <xref:System.Net.Http.IHttpClientFactory> can be registered and used to configure and create <xref:System.Net.Http.HttpClient> instances in an app.</span></span> <span data-ttu-id="f9e53-328">이는 다음과 같은 이점을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-328">It offers the following benefits:</span></span>

* <span data-ttu-id="f9e53-329">논리적 `HttpClient` 인스턴스를 구성하고 이름을 지정하기 위한 중앙 위치를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-329">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="f9e53-330">예를 들어, [GitHub](https://github.com/)에 액세스하는 *github* 클라이언트를 등록 및 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-330">For example, a *github* client can be registered and configured to access [GitHub](https://github.com/).</span></span> <span data-ttu-id="f9e53-331">기본 클라이언트는 다른 용도로 등록할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-331">A default client can be registered for other purposes.</span></span>
* <span data-ttu-id="f9e53-332">`HttpClient`에서 위임 처리기를 통해 나가는 미들웨어의 개념을 체계화하고 Polly 기반 미들웨어에 대한 확장을 제공하여 이를 활용합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-332">Codifies the concept of outgoing middleware via delegating handlers in `HttpClient` and provides extensions for Polly-based middleware to take advantage of that.</span></span>
* <span data-ttu-id="f9e53-333">`HttpClient` 수명을 수동으로 관리할 때 발생하는 일반적인 DNS 문제를 피하기 위해 기본 `HttpClientMessageHandler` 인스턴스의 풀링 및 수명을 관리합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-333">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances to avoid common DNS problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="f9e53-334">팩터리에서 만든 클라이언트를 통해 전송된 모든 요청에 대해 구성 가능한 로깅 경험(`ILogger`을 통해)을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-334">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="f9e53-335">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="f9e53-335">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="consumption-patterns"></a><span data-ttu-id="f9e53-336">사용 패턴</span><span class="sxs-lookup"><span data-stu-id="f9e53-336">Consumption patterns</span></span>

<span data-ttu-id="f9e53-337">앱에서 `IHttpClientFactory`를 사용할 수 있는 몇 가지 방법이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-337">There are several ways `IHttpClientFactory` can be used in an app:</span></span>

* [<span data-ttu-id="f9e53-338">기본적인 사용 방법</span><span class="sxs-lookup"><span data-stu-id="f9e53-338">Basic usage</span></span>](#basic-usage)
* [<span data-ttu-id="f9e53-339">명명된 클라이언트</span><span class="sxs-lookup"><span data-stu-id="f9e53-339">Named clients</span></span>](#named-clients)
* [<span data-ttu-id="f9e53-340">형식화된 클라이언트</span><span class="sxs-lookup"><span data-stu-id="f9e53-340">Typed clients</span></span>](#typed-clients)
* [<span data-ttu-id="f9e53-341">생성된 클라이언트</span><span class="sxs-lookup"><span data-stu-id="f9e53-341">Generated clients</span></span>](#generated-clients)

<span data-ttu-id="f9e53-342">어떤 방법도 다른 방법에 비해 절대적으로 우수하지는 않습니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-342">None of them are strictly superior to another.</span></span> <span data-ttu-id="f9e53-343">가장 좋은 방법은 앱의 제약 조건에 따라서 달라집니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-343">The best approach depends upon the app's constraints.</span></span>

### <a name="basic-usage"></a><span data-ttu-id="f9e53-344">기본적인 사용 방법</span><span class="sxs-lookup"><span data-stu-id="f9e53-344">Basic usage</span></span>

<span data-ttu-id="f9e53-345">`IHttpClientFactory`는 `Startup.ConfigureServices` 메서드 내에서 `IServiceCollection`의 `AddHttpClient` 확장 메서드를 호출하여 등록할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-345">The `IHttpClientFactory` can be registered by calling the `AddHttpClient` extension method on the `IServiceCollection`, inside the `Startup.ConfigureServices` method.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

<span data-ttu-id="f9e53-346">일단 등록하고 나면 코드는 [DI(종속성 주입)](xref:fundamentals/dependency-injection)를 사용하여 서비스를 주입할 수 있는 모든 곳에서 `IHttpClientFactory`를 받을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-346">Once registered, code can accept an `IHttpClientFactory` anywhere services can be injected with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="f9e53-347">`IHttpClientFactory`을(를) 사용하여 `HttpClient` 인스턴스를 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-347">The `IHttpClientFactory` can be used to create an `HttpClient` instance:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

<span data-ttu-id="f9e53-348">이러한 방식으로 `IHttpClientFactory`를 사용하는 것은 기존 앱을 리팩터링할 수 있는 좋은 방법입니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-348">Using `IHttpClientFactory` in this fashion is a good way to refactor an existing app.</span></span> <span data-ttu-id="f9e53-349">`HttpClient`가 사용되는 방식에는 어떠한 영향도 없습니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-349">It has no impact on the way `HttpClient` is used.</span></span> <span data-ttu-id="f9e53-350">`HttpClient` 인스턴스가 현재 만들어지는 위치에서 해당 코드를 <xref:System.Net.Http.IHttpClientFactory.CreateClient*>에 대한 호출로 대체합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-350">In places where `HttpClient` instances are currently created, replace those occurrences with a call to <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span></span>

### <a name="named-clients"></a><span data-ttu-id="f9e53-351">명명된 클라이언트</span><span class="sxs-lookup"><span data-stu-id="f9e53-351">Named clients</span></span>

<span data-ttu-id="f9e53-352">앱이 각각 다른 구성을 사용하는 많은 개별적인 `HttpClient`의 사용을 필요로 하는 경우 선택할 수 있는 방법은 **명명된 클라이언트**를 사용하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-352">If an app requires many distinct uses of `HttpClient`, each with a different configuration, an option is to use **named clients**.</span></span> <span data-ttu-id="f9e53-353">명명된 `HttpClient`에 대한 구성은 `Startup.ConfigureServices`에서 등록하는 동안 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-353">Configuration for a named `HttpClient` can be specified during registration in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

<span data-ttu-id="f9e53-354">위의 코드에서는 `AddHttpClient`를 호출하여 *github*이라는 이름을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-354">In the preceding code, `AddHttpClient` is called, providing the name *github*.</span></span> <span data-ttu-id="f9e53-355">이 클라이언트에는 일부 기본 구성&mdash;즉 GitHub API를 작동하는 데 필요한 기준 주소 및 두 개의 헤더가 적용되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-355">This client has some default configuration applied&mdash;namely the base address and two headers required to work with the GitHub API.</span></span>

<span data-ttu-id="f9e53-356">`CreateClient`를 호출할 때마다 `HttpClient`의 새 인스턴스를 만들고 구성 작업을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-356">Each time `CreateClient` is called, a new instance of `HttpClient` is created and the configuration action is called.</span></span>

<span data-ttu-id="f9e53-357">명명된 클라이언트를 사용하기 위해서 `CreateClient`에 문자열 매개 변수를 전달할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-357">To consume a named client, a string parameter can be passed to `CreateClient`.</span></span> <span data-ttu-id="f9e53-358">만들 클라이언트의 이름을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-358">Specify the name of the client to be created:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

<span data-ttu-id="f9e53-359">위의 코드에서는 요청이 호스트 이름을 지정할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-359">In the preceding code, the request doesn't need to specify a hostname.</span></span> <span data-ttu-id="f9e53-360">클라이언트에 대해 구성된 기본 주소를 사용하므로 경로만 전달할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-360">It can pass just the path, since the base address configured for the client is used.</span></span>

### <a name="typed-clients"></a><span data-ttu-id="f9e53-361">형식화된 클라이언트</span><span class="sxs-lookup"><span data-stu-id="f9e53-361">Typed clients</span></span>

<span data-ttu-id="f9e53-362">형식화된 클라이언트:</span><span class="sxs-lookup"><span data-stu-id="f9e53-362">Typed clients:</span></span>

* <span data-ttu-id="f9e53-363">문자열을 키로 사용할 필요가 없이 명명된 클라이언트와 동일한 기능을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-363">Provide the same capabilities as named clients without the need to use strings as keys.</span></span>
* <span data-ttu-id="f9e53-364">클라이언트를 사용할 때 IntelliSense 및 컴파일러 도움말을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-364">Provides IntelliSense and compiler help when consuming clients.</span></span>
* <span data-ttu-id="f9e53-365">특정 `HttpClient`을 구성하고 상호 작용하기 위해 단일 위치를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-365">Provide a single location to configure and interact with a particular `HttpClient`.</span></span> <span data-ttu-id="f9e53-366">예를 들어 단일 형식화된 클라이언트는 단일 백 엔드 엔드포인트에 사용될 수 있으며 해당 엔드포인트를 다루는 모든 논리를 캡슐화할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-366">For example, a single typed client might be used for a single backend endpoint and encapsulate all logic dealing with that endpoint.</span></span>
* <span data-ttu-id="f9e53-367">DI로 작업하고 앱에서 필요할 경우 삽입할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-367">Work with DI and can be injected where required in your app.</span></span>

<span data-ttu-id="f9e53-368">형식화된 클라이언트는 생성자에서 `HttpClient` 매개 변수를 받습니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-368">A typed client accepts an `HttpClient` parameter in its constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]

<span data-ttu-id="f9e53-369">위의 코드에서는 구성이 형식화된 클라이언트로 이동되었습니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-369">In the preceding code, the configuration is moved into the typed client.</span></span> <span data-ttu-id="f9e53-370">`HttpClient` 개체는 공용 속성으로 노출됩니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-370">The `HttpClient` object is exposed as a public property.</span></span> <span data-ttu-id="f9e53-371">`HttpClient` 기능을 노출하는 API 특정 메서드를 정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-371">It's possible to define API-specific methods that expose `HttpClient` functionality.</span></span> <span data-ttu-id="f9e53-372">`GetAspNetDocsIssues` 메서드는 GitHub 리포지토리에서 공개된 최신 문제를 구문 분석하고 쿼리하는 데 필요한 코드를 캡슐화합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-372">The `GetAspNetDocsIssues` method encapsulates the code needed to query for and parse out the latest open issues from a GitHub repository.</span></span>

<span data-ttu-id="f9e53-373">형식화된 클라이언트를 등록하기 위해서 `Startup.ConfigureServices`에서 형식화된 클라이언트 클래스를 지정하여 제네릭 <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> 확장 메서드를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-373">To register a typed client, the generic <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> extension method can be used within `Startup.ConfigureServices`, specifying the typed client class:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

<span data-ttu-id="f9e53-374">형식화된 클라이언트는 DI를 사용하여 일시적으로 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-374">The typed client is registered as transient with DI.</span></span> <span data-ttu-id="f9e53-375">형식화된 클라이언트는 직접 주입되고 사용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-375">The typed client can be injected and consumed directly:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

<span data-ttu-id="f9e53-376">원한다면 형식화된 클라이언트의 생성자 대신 `Startup.ConfigureServices`에서 등록하는 동안 형식화된 클라이언트에 대한 구성을 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-376">If preferred, the configuration for a typed client can be specified during registration in `Startup.ConfigureServices`, rather than in the typed client's constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

<span data-ttu-id="f9e53-377">형식화된 클라이언트 내에서 `HttpClient`를 완전히 캡슐화할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-377">It's possible to entirely encapsulate the `HttpClient` within a typed client.</span></span> <span data-ttu-id="f9e53-378">속성으로 노출하는 대신 공용 메서드를 제공하여 내부적으로 `HttpClient` 인스턴스를 호출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-378">Rather than exposing it as a property, public methods can be provided which call the `HttpClient` instance internally.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

<span data-ttu-id="f9e53-379">위의 코드에서는 `HttpClient`가 전용 필드로 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-379">In the preceding code, the `HttpClient` is stored as a private field.</span></span> <span data-ttu-id="f9e53-380">외부 호출을 하기 위한 모든 액세스는 `GetRepos` 메서드를 거칩니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-380">All access to make external calls goes through the `GetRepos` method.</span></span>

### <a name="generated-clients"></a><span data-ttu-id="f9e53-381">생성된 클라이언트</span><span class="sxs-lookup"><span data-stu-id="f9e53-381">Generated clients</span></span>

<span data-ttu-id="f9e53-382">`IHttpClientFactory`는 [Refit](https://github.com/paulcbetts/refit)과 같은 다른 타사 라이브러리와 함께 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-382">`IHttpClientFactory` can be used in combination with other third-party libraries such as [Refit](https://github.com/paulcbetts/refit).</span></span> <span data-ttu-id="f9e53-383">Refit은 .NET용 REST 라이브러리입니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-383">Refit is a REST library for .NET.</span></span> <span data-ttu-id="f9e53-384">REST API를 라이브 인터페이스로 변환합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-384">It converts REST APIs into live interfaces.</span></span> <span data-ttu-id="f9e53-385">인터페이스의 구현은 `HttpClient`를 사용하여 외부 HTTP를 호출하도록 `RestService`에 의해 동적으로 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-385">An implementation of the interface is generated dynamically by the `RestService`, using `HttpClient` to make the external HTTP calls.</span></span>

<span data-ttu-id="f9e53-386">외부 API와 해당 응답을 나타내기 위한 인터페이스와 회신이 정의됩니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-386">An interface and a reply are defined to represent the external API and its response:</span></span>

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

<span data-ttu-id="f9e53-387">구현을 생성하기 위해 Refit를 사용하여 형식화된 클라이언트를 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-387">A typed client can be added, using Refit to generate the implementation:</span></span>

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

<span data-ttu-id="f9e53-388">DI 및 Refit에서 제공한 구현을 통해 필요한 곳에서 정의된 인터페이스를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-388">The defined interface can be consumed where necessary, with the implementation provided by DI and Refit:</span></span>

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

## <a name="outgoing-request-middleware"></a><span data-ttu-id="f9e53-389">나가는 요청 미들웨어</span><span class="sxs-lookup"><span data-stu-id="f9e53-389">Outgoing request middleware</span></span>

<span data-ttu-id="f9e53-390">`HttpClient`에는 나가는 HTTP 요청을 위해 함께 연결될 수 있는 위임 처리기라는 개념이 이미 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-390">`HttpClient` already has the concept of delegating handlers that can be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="f9e53-391">`IHttpClientFactory`를 사용하면 각 명명된 클라이언트에 적용할 처리기를 쉽게 정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-391">The `IHttpClientFactory` makes it easy to define the handlers to apply for each named client.</span></span> <span data-ttu-id="f9e53-392">나가는 요청 미들웨어 파이프라인을 만들기 위한 여러 처리기의 등록 및 연결을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-392">It supports registration and chaining of multiple handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="f9e53-393">이러한 처리기 각각은 나가는 요청 전후에 작업을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-393">Each of these handlers is able to perform work before and after the outgoing request.</span></span> <span data-ttu-id="f9e53-394">이 패턴은 ASP.NET Core의 인바운드 미들웨어 파이프라인과 비슷합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-394">This pattern is similar to the inbound middleware pipeline in ASP.NET Core.</span></span> <span data-ttu-id="f9e53-395">이 패턴은 캐싱, 오류 처리, 직렬화 및 로깅을 포함한 HTTP 요청을 둘러싼 횡단 관심사를 관리하기 위한 메커니즘을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-395">The pattern provides a mechanism to manage cross-cutting concerns around HTTP requests, including caching, error handling, serialization, and logging.</span></span>

<span data-ttu-id="f9e53-396">처리기를 만들려면 <xref:System.Net.Http.DelegatingHandler>에서 파생되는 클래스를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-396">To create a handler, define a class deriving from <xref:System.Net.Http.DelegatingHandler>.</span></span> <span data-ttu-id="f9e53-397">파이프라인의 다음 처리기로 요청을 전달하기 전에 코드를 실행하려면 `SendAsync` 메서드를 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-397">Override the `SendAsync` method to execute code before passing the request to the next handler in the pipeline:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

<span data-ttu-id="f9e53-398">위의 코드에서는 기본 처리기를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-398">The preceding code defines a basic handler.</span></span> <span data-ttu-id="f9e53-399">`X-API-KEY` 헤더가 요청에 포함되었는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-399">It checks to see if an `X-API-KEY` header has been included on the request.</span></span> <span data-ttu-id="f9e53-400">헤더가 누락된 경우 HTTP 호출을 방지하고 적합한 응답을 반환할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-400">If the header is missing, it can avoid the HTTP call and return a suitable response.</span></span>

<span data-ttu-id="f9e53-401">등록하는 동안 하나 이상의 처리기를 `HttpClient`의 구성에 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-401">During registration, one or more handlers can be added to the configuration for an `HttpClient`.</span></span> <span data-ttu-id="f9e53-402">이 작업은 <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>의 확장 메서드를 통해 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-402">This task is accomplished via extension methods on the <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet5)]

<span data-ttu-id="f9e53-403">위의 코드에서 `ValidateHeaderHandler`는 DI에 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-403">In the preceding code, the `ValidateHeaderHandler` is registered with DI.</span></span> <span data-ttu-id="f9e53-404">`IHttpClientFactory`는 각 처리기에 대해 별도의 DI 범위를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-404">The `IHttpClientFactory` creates a separate DI scope for each handler.</span></span> <span data-ttu-id="f9e53-405">처리기는 모든 범위의 서비스에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-405">Handlers are free to depend upon services of any scope.</span></span> <span data-ttu-id="f9e53-406">처리기가 삭제되면 처리기가 사용하는 서비스가 삭제됩니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-406">Services that handlers depend upon are disposed when the handler is disposed.</span></span>

<span data-ttu-id="f9e53-407">일단 등록되면 처리기의 형식을 전달하여 <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*>를 호출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-407">Once registered, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> can be called, passing in the type for the handler.</span></span>

<span data-ttu-id="f9e53-408">여러 처리기를 실행해야 하는 순서에 따라 등록할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-408">Multiple handlers can be registered in the order that they should execute.</span></span> <span data-ttu-id="f9e53-409">각 처리기는 최종 `HttpClientHandler`가 요청을 실행할 때까지 다음 처리기를 래핑합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-409">Each handler wraps the next handler until the final `HttpClientHandler` executes the request:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

<span data-ttu-id="f9e53-410">다음 방법 중 하나를 사용하여 메시지 처리기와 요청별 상태를 공유하세요.</span><span class="sxs-lookup"><span data-stu-id="f9e53-410">Use one of the following approaches to share per-request state with message handlers:</span></span>

* <span data-ttu-id="f9e53-411">`HttpRequestMessage.Properties`를 사용하여 데이터를 처리기로 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-411">Pass data into the handler using `HttpRequestMessage.Properties`.</span></span>
* <span data-ttu-id="f9e53-412">`IHttpContextAccessor`를 사용하여 현재 요청에 액세스합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-412">Use `IHttpContextAccessor` to access the current request.</span></span>
* <span data-ttu-id="f9e53-413">사용자 지정 `AsyncLocal` 스토리지 개체를 만들어 데이터를 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-413">Create a custom `AsyncLocal` storage object to pass the data.</span></span>

## <a name="use-polly-based-handlers"></a><span data-ttu-id="f9e53-414">Polly 기반 처리기 사용</span><span class="sxs-lookup"><span data-stu-id="f9e53-414">Use Polly-based handlers</span></span>

<span data-ttu-id="f9e53-415">`IHttpClientFactory`는 [Polly](https://github.com/App-vNext/Polly)라는 유명한 타사 라이브러리와 통합됩니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-415">`IHttpClientFactory` integrates with a popular third-party library called [Polly](https://github.com/App-vNext/Polly).</span></span> <span data-ttu-id="f9e53-416">Polly는 .NET용 포괄적인 회복탄력성 및 일시적 오류 처리 라이브러리입니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-416">Polly is a comprehensive resilience and transient fault-handling library for .NET.</span></span> <span data-ttu-id="f9e53-417">개발자는 이를 사용하여 재시도, 회로 차단기, 시간 초과, 격벽 격리 및 대체(Fallback) 같은 정책을 유연하고 스레드로부터 안전한 방식으로 표현할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-417">It allows developers to express policies such as Retry, Circuit Breaker, Timeout, Bulkhead Isolation, and Fallback in a fluent and thread-safe manner.</span></span>

<span data-ttu-id="f9e53-418">구성된 `HttpClient` 인스턴스를 통해 Polly 정책을 사용할 수 있도록 확장 메서드가 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-418">Extension methods are provided to enable the use of Polly policies with configured `HttpClient` instances.</span></span> <span data-ttu-id="f9e53-419">Polly 확장은:</span><span class="sxs-lookup"><span data-stu-id="f9e53-419">The Polly extensions:</span></span>

* <span data-ttu-id="f9e53-420">클라이언트에 Polly 기반 처리기 추가를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-420">Support adding Polly-based handlers to clients.</span></span>
* <span data-ttu-id="f9e53-421">[Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet 패키지를 설치한 후 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-421">Can be used after installing the [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet package.</span></span> <span data-ttu-id="f9e53-422">이 패키지는 ASP.NET Core 공유 프레임워크에 포함되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-422">The package isn't included in the ASP.NET Core shared framework.</span></span>

### <a name="handle-transient-faults"></a><span data-ttu-id="f9e53-423">일시적인 오류 처리</span><span class="sxs-lookup"><span data-stu-id="f9e53-423">Handle transient faults</span></span>

<span data-ttu-id="f9e53-424">가장 일반적인 오류는 외부 HTTP 호출이 일시적인 경우 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-424">Most common faults occur when external HTTP calls are transient.</span></span> <span data-ttu-id="f9e53-425">일시적인 오류를 처리하기 위한 정책을 정의할 수 있는 `AddTransientHttpErrorPolicy`라는 편리한 확장 메서드가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-425">A convenient extension method called `AddTransientHttpErrorPolicy` is included which allows a policy to be defined to handle transient errors.</span></span> <span data-ttu-id="f9e53-426">이 확장 메서드로 구성된 정책은 `HttpRequestException`, HTTP 5xx 응답 및 HTTP 408 응답을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-426">Policies configured with this extension method handle `HttpRequestException`, HTTP 5xx responses, and HTTP 408 responses.</span></span>

<span data-ttu-id="f9e53-427">`AddTransientHttpErrorPolicy` 확장은 `Startup.ConfigureServices` 내에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-427">The `AddTransientHttpErrorPolicy` extension can be used within `Startup.ConfigureServices`.</span></span> <span data-ttu-id="f9e53-428">이 확장은 가능한 일시적 오류를 나타내는 오류를 처리하기 위해 구성된 `PolicyBuilder` 개체에 대한 액세스를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-428">The extension provides access to a `PolicyBuilder` object configured to handle errors representing a possible transient fault:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet7)]

<span data-ttu-id="f9e53-429">위의 코드에서는 `WaitAndRetryAsync` 정책을 정의하고 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-429">In the preceding code, a `WaitAndRetryAsync` policy is defined.</span></span> <span data-ttu-id="f9e53-430">실패한 요청은 최대 세 번까지 다시 시도되며 시도 간 600밀리초의 지연 간격을 둡니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-430">Failed requests are retried up to three times with a delay of 600 ms between attempts.</span></span>

### <a name="dynamically-select-policies"></a><span data-ttu-id="f9e53-431">동적으로 정책 선택</span><span class="sxs-lookup"><span data-stu-id="f9e53-431">Dynamically select policies</span></span>

<span data-ttu-id="f9e53-432">Polly 기반 처리기를 추가하는 데 사용할 수 있는 추가 확장 메서드가 존재합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-432">Additional extension methods exist which can be used to add Polly-based handlers.</span></span> <span data-ttu-id="f9e53-433">이러한 확장 중 하나는 여러 오버로드가 있는 `AddPolicyHandler`입니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-433">One such extension is `AddPolicyHandler`, which has multiple overloads.</span></span> <span data-ttu-id="f9e53-434">한 오버로드는 적용할 정책을 정의할 때 요청을 검사할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-434">One overload allows the request to be inspected when defining which policy to apply:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

<span data-ttu-id="f9e53-435">위의 코드에서는 나가는 요청이 HTTP GET인 경우 10초 시간 제한이 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-435">In the preceding code, if the outgoing request is an HTTP GET, a 10-second timeout is applied.</span></span> <span data-ttu-id="f9e53-436">다른 HTTP 메서드의 경우 30초 시간 제한이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-436">For any other HTTP method, a 30-second timeout is used.</span></span>

### <a name="add-multiple-polly-handlers"></a><span data-ttu-id="f9e53-437">여러 Polly 처리기 추가</span><span class="sxs-lookup"><span data-stu-id="f9e53-437">Add multiple Polly handlers</span></span>

<span data-ttu-id="f9e53-438">향상된 기능을 제공하기 위해 Polly 정책을 중첩하는 것이 일반적입니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-438">It's common to nest Polly policies to provide enhanced functionality:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

<span data-ttu-id="f9e53-439">위의 예제에서는 두 개의 처리기가 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-439">In the preceding example, two handlers are added.</span></span> <span data-ttu-id="f9e53-440">첫 번째 처리기는 재시도 정책을 추가하기 위해 `AddTransientHttpErrorPolicy` 확장을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-440">The first uses the `AddTransientHttpErrorPolicy` extension to add a retry policy.</span></span> <span data-ttu-id="f9e53-441">실패한 요청은 최대 세 번까지 다시 시도됩니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-441">Failed requests are retried up to three times.</span></span> <span data-ttu-id="f9e53-442">`AddTransientHttpErrorPolicy`에 대한 두 번째 호출은 회로 차단기 정책을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-442">The second call to `AddTransientHttpErrorPolicy` adds a circuit breaker policy.</span></span> <span data-ttu-id="f9e53-443">5번의 시도가 순차적으로 실패하는 경우 추가적인 외부 요청은 30초 동안 차단됩니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-443">Further external requests are blocked for 30 seconds if five failed attempts occur sequentially.</span></span> <span data-ttu-id="f9e53-444">회로 차단기 정책은 상태를 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-444">Circuit breaker policies are stateful.</span></span> <span data-ttu-id="f9e53-445">이 클라이언트를 통한 모든 호출은 동일한 회로 상태를 공유합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-445">All calls through this client share the same circuit state.</span></span>

### <a name="add-policies-from-the-polly-registry"></a><span data-ttu-id="f9e53-446">Polly 레지스트리로부터 정책 추가</span><span class="sxs-lookup"><span data-stu-id="f9e53-446">Add policies from the Polly registry</span></span>

<span data-ttu-id="f9e53-447">정기적으로 사용되는 정책을 관리하는 방법은 정책을 한 번 정의하고 이를 `PolicyRegistry`에 등록하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-447">An approach to managing regularly used policies is to define them once and register them with a `PolicyRegistry`.</span></span> <span data-ttu-id="f9e53-448">레지스트리의 정책을 사용하여 처리기를 추가할 수 있는 확장 메서드가 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-448">An extension method is provided which allows a handler to be added using a policy from the registry:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet10)]

<span data-ttu-id="f9e53-449">위의 코드에서는 `PolicyRegistry`가 `ServiceCollection`에 추가될 때 두 가지 정책이 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-449">In the preceding code, two policies are registered when the `PolicyRegistry` is added to the `ServiceCollection`.</span></span> <span data-ttu-id="f9e53-450">레지스트리의 정책을 사용하기 위해서는 적용할 정책의 이름을 전달하여 `AddPolicyHandlerFromRegistry` 메서드를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-450">To use a policy from the registry, the `AddPolicyHandlerFromRegistry` method is used, passing the name of the policy to apply.</span></span>

<span data-ttu-id="f9e53-451">`IHttpClientFactory` 및 Polly 통합에 대한 추가 정보는 [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory)에서 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-451">Further information about `IHttpClientFactory` and Polly integrations can be found on the [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span></span>

## <a name="httpclient-and-lifetime-management"></a><span data-ttu-id="f9e53-452">HttpClient 및 수명 관리</span><span class="sxs-lookup"><span data-stu-id="f9e53-452">HttpClient and lifetime management</span></span>

<span data-ttu-id="f9e53-453">`IHttpClientFactory`에서 `CreateClient`가 호출될 때마다 새로운 `HttpClient` 인스턴스가 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-453">A new `HttpClient` instance is returned each time `CreateClient` is called on the `IHttpClientFactory`.</span></span> <span data-ttu-id="f9e53-454">명명된 클라이언트마다 <xref:System.Net.Http.HttpMessageHandler>가 존재합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-454">There's an <xref:System.Net.Http.HttpMessageHandler> per named client.</span></span> <span data-ttu-id="f9e53-455">팩터리는 `HttpMessageHandler` 인스턴스의 수명을 관리합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-455">The factory manages the lifetimes of the `HttpMessageHandler` instances.</span></span>

<span data-ttu-id="f9e53-456">`IHttpClientFactory`는 리소스 사용을 줄이기 위해 팩터리에서 만든 `HttpMessageHandler` 인스턴스를 풀링합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-456">`IHttpClientFactory` pools the `HttpMessageHandler` instances created by the factory to reduce resource consumption.</span></span> <span data-ttu-id="f9e53-457">수명이 만료되지 않은 경우, 새 `HttpClient` 인스턴스를 만들 때 풀에서 `HttpMessageHandler` 인스턴스가 재사용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-457">An `HttpMessageHandler` instance may be reused from the pool when creating a new `HttpClient` instance if its lifetime hasn't expired.</span></span>

<span data-ttu-id="f9e53-458">일반적으로 각 처리기는 자체적인 기본 HTTP 연결을 관리하므로 처리기의 풀링이 적합합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-458">Pooling of handlers is desirable as each handler typically manages its own underlying HTTP connections.</span></span> <span data-ttu-id="f9e53-459">필요한 것보다 많은 처리기를 만들면 연결 지연이 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-459">Creating more handlers than necessary can result in connection delays.</span></span> <span data-ttu-id="f9e53-460">또한 일부 처리기는 무한정으로 연결을 열어 놓아 처리기가 DNS 변경에 대응하는 것을 막을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-460">Some handlers also keep connections open indefinitely, which can prevent the handler from reacting to DNS changes.</span></span>

<span data-ttu-id="f9e53-461">기본 처리기 수명은 2분입니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-461">The default handler lifetime is two minutes.</span></span> <span data-ttu-id="f9e53-462">명명된 클라이언트별 기준으로 기본값을 재정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-462">The default value can be overridden on a per named client basis.</span></span> <span data-ttu-id="f9e53-463">이를 재정의하려면 클라이언트를 만들 때 반환되는 `IHttpClientBuilder`에서 <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*>을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-463">To override it, call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> on the `IHttpClientBuilder` that is returned when creating the client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet11)]

<span data-ttu-id="f9e53-464">클라이언트의 삭제는 불필요합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-464">Disposal of the client isn't required.</span></span> <span data-ttu-id="f9e53-465">삭제는 나가는 요청을 취소하고 <xref:System.IDisposable.Dispose*>를 호출한 후에는 지정된 `HttpClient` 인스턴스가 사용될 수 없도록 보장합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-465">Disposal cancels outgoing requests and guarantees the given `HttpClient` instance can't be used after calling <xref:System.IDisposable.Dispose*>.</span></span> <span data-ttu-id="f9e53-466">`IHttpClientFactory`는 `HttpClient` 인스턴스에서 사용되는 리소스를 추적하고 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-466">`IHttpClientFactory` tracks and disposes resources used by `HttpClient` instances.</span></span> <span data-ttu-id="f9e53-467">`HttpClient` 인스턴스는 일반적으로 삭제가 필요하지 않은 .NET 개체로 간주할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-467">The `HttpClient` instances can generally be treated as .NET objects not requiring disposal.</span></span>

<span data-ttu-id="f9e53-468">장기간 단일 `HttpClient` 인스턴스를 활성 상태로 유지하는 것은 `IHttpClientFactory`가 등장하기 전에 사용되던 일반적인 패턴입니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-468">Keeping a single `HttpClient` instance alive for a long duration is a common pattern used before the inception of `IHttpClientFactory`.</span></span> <span data-ttu-id="f9e53-469">이 패턴은 `IHttpClientFactory`로 마이그레이션한 이후에는 불필요합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-469">This pattern becomes unnecessary after migrating to `IHttpClientFactory`.</span></span>

### <a name="alternatives-to-ihttpclientfactory"></a><span data-ttu-id="f9e53-470">IHttpClientFactory의 대안</span><span class="sxs-lookup"><span data-stu-id="f9e53-470">Alternatives to IHttpClientFactory</span></span>

<span data-ttu-id="f9e53-471">DI 지원 앱에서 `IHttpClientFactory`을(를) 사용하면 다음이 방지됩니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-471">Using `IHttpClientFactory` in a DI-enabled app avoids:</span></span>

* <span data-ttu-id="f9e53-472">`HttpMessageHandler` 인스턴스를 풀링하여 리소스 소모 문제가 발생했습니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-472">Resource exhaustion problems by pooling `HttpMessageHandler` instances.</span></span>
* <span data-ttu-id="f9e53-473">정기적으로 `HttpMessageHandler` 인스턴스를 순환하여 오래된 DNS 문제가 발생했습니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-473">Stale DNS problems by cycling `HttpMessageHandler` instances at regular intervals.</span></span>

<span data-ttu-id="f9e53-474">수명이 긴 <xref:System.Net.Http.SocketsHttpHandler> 인스턴스를 사용하여 위의 문제를 해결하는 다른 방법이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-474">There are alternative ways to solve the preceding problems using a long-lived <xref:System.Net.Http.SocketsHttpHandler> instance.</span></span>

- <span data-ttu-id="f9e53-475">앱 시작 시 `SocketsHttpHandler`의 인스턴스를 만들고 앱 수명 동안 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-475">Create an instance of `SocketsHttpHandler` when the app starts and use it for the life of the app.</span></span>
- <span data-ttu-id="f9e53-476">DNS 새로 고침 시간에 따라 적절한 값으로 <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime>을(를) 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-476">Configure <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> to an appropriate value based on DNS refresh times.</span></span>
- <span data-ttu-id="f9e53-477">필요에 따라 `new HttpClient(handler, disposeHandler: false)`을(를) 사용하여 `HttpClient` 인스턴스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-477">Create `HttpClient` instances using `new HttpClient(handler, disposeHandler: false)` as needed.</span></span>

<span data-ttu-id="f9e53-478">위의 방법은 비슷한 방식으로 `IHttpClientFactory`에서 해결하는 리소스 관리 문제를 해결합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-478">The preceding approaches solve the resource management problems that `IHttpClientFactory` solves in a similar way.</span></span>

- <span data-ttu-id="f9e53-479">`SocketsHttpHandler`은(는) `HttpClient` 인스턴스 간에 연결을 공유합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-479">The `SocketsHttpHandler` shares connections across `HttpClient` instances.</span></span> <span data-ttu-id="f9e53-480">이와 같이 공유하면 소켓이 소모되지 않도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-480">This sharing prevents socket exhaustion.</span></span>
- <span data-ttu-id="f9e53-481">오래된 DNS 문제를 방지하기 위해 `SocketsHttpHandler`에서 `PooledConnectionLifetime`에 따라 연결을 순환합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-481">The `SocketsHttpHandler` cycles connections according to `PooledConnectionLifetime` to avoid stale DNS problems.</span></span>

### <a name="cookies"></a><span data-ttu-id="f9e53-482">쿠키</span><span class="sxs-lookup"><span data-stu-id="f9e53-482">Cookies</span></span>

<span data-ttu-id="f9e53-483">풀링된 `HttpMessageHandler` 인스턴스는 `CookieContainer` 개체를 공유합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-483">The pooled `HttpMessageHandler` instances results in `CookieContainer` objects being shared.</span></span> <span data-ttu-id="f9e53-484">예상치 못한 `CookieContainer` 개체 공유로 잘못된 코드가 발생하는 경우가 많습니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-484">Unanticipated `CookieContainer` object sharing often results in incorrect code.</span></span> <span data-ttu-id="f9e53-485">쿠키가 필요한 앱의 경우 다음 중 하나를 고려하세요.</span><span class="sxs-lookup"><span data-stu-id="f9e53-485">For apps that require cookies, consider either:</span></span>

 - <span data-ttu-id="f9e53-486">자동 쿠키 처리 사용 안 함</span><span class="sxs-lookup"><span data-stu-id="f9e53-486">Disabling automatic cookie handling</span></span>
 - <span data-ttu-id="f9e53-487">`IHttpClientFactory` 방지</span><span class="sxs-lookup"><span data-stu-id="f9e53-487">Avoiding `IHttpClientFactory`</span></span>

<span data-ttu-id="f9e53-488"><xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*>을(를) 호출하여 자동 쿠키 처리를 사용하지 않도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-488">Call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> to disable automatic cookie handling:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a><span data-ttu-id="f9e53-489">로깅</span><span class="sxs-lookup"><span data-stu-id="f9e53-489">Logging</span></span>

<span data-ttu-id="f9e53-490">`IHttpClientFactory`을 통해 만든 클라이언트는 모든 요청에 대한 로그 메시지를 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-490">Clients created via `IHttpClientFactory` record log messages for all requests.</span></span> <span data-ttu-id="f9e53-491">기본 로그 메시지를 보려면 로깅 구성에서 적절한 정보 수준을 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-491">Enable the appropriate information level in your logging configuration to see the default log messages.</span></span> <span data-ttu-id="f9e53-492">요청 헤더의 로깅 등과 같은 추가 로깅은 추적 수준에서만 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-492">Additional logging, such as the logging of request headers, is only included at trace level.</span></span>

<span data-ttu-id="f9e53-493">각 클라이언트에 사용되는 로그 범주는 클라이언트의 이름을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-493">The log category used for each client includes the name of the client.</span></span> <span data-ttu-id="f9e53-494">예를 들어, *MyNamedClient*라는 클라이언트는 `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`의 범주를 사용하여 메시지를 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-494">A client named *MyNamedClient*, for example, logs messages with a category of `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`.</span></span> <span data-ttu-id="f9e53-495">*LogicalHandler*라는 접미사가 붙은 메시지는 요청 처리기 파이프라인 외부에서 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-495">Messages suffixed with *LogicalHandler* occur outside the request handler pipeline.</span></span> <span data-ttu-id="f9e53-496">요청 시 파이프라인의 다른 모든 처리기에서 이를 처리하기 전에 메시지가 기록됩니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-496">On the request, messages are logged before any other handlers in the pipeline have processed it.</span></span> <span data-ttu-id="f9e53-497">응답 시 다른 모든 파이프라인 처리기가 응답을 받은 후에 메시지가 기록됩니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-497">On the response, messages are logged after any other pipeline handlers have received the response.</span></span>

<span data-ttu-id="f9e53-498">로깅은 요청 처리기 파이프라인 내부에서도 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-498">Logging also occurs inside the request handler pipeline.</span></span> <span data-ttu-id="f9e53-499">*MyNamedClient* 예제에서 해당 메시지는 로그 범주 `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`에 대해 기록됩니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-499">In the *MyNamedClient* example, those messages are logged against the log category `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`.</span></span> <span data-ttu-id="f9e53-500">요청의 경우 이는 요청이 네트워크에서 전송되기 직전 및 다른 모든 처리기가 실행된 후에 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-500">For the request, this occurs after all other handlers have run and immediately before the request is sent out on the network.</span></span> <span data-ttu-id="f9e53-501">응답 시 이 로깅은 처리기 파이프라인을 통해 응답이 다시 전달되기 전의 응답 상태를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-501">On the response, this logging includes the state of the response before it passes back through the handler pipeline.</span></span>

<span data-ttu-id="f9e53-502">파이프라인 외부 및 내부에서 로깅을 사용하도록 설정하면 다른 파이프라인 처리기가 수행한 변경 내용을 검사할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-502">Enabling logging outside and inside the pipeline enables inspection of the changes made by the other pipeline handlers.</span></span> <span data-ttu-id="f9e53-503">예를 들어 여기에는 요청 헤더 또는 응답 상태 코드에 대한 변경이 포함될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-503">This may include changes to request headers, for example, or to the response status code.</span></span>

<span data-ttu-id="f9e53-504">로그 범주에 클라이언트의 이름을 포함하면 필요한 경우 명명된 특정 클라이언트에 대한 로그 필터링을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-504">Including the name of the client in the log category enables log filtering for specific named clients where necessary.</span></span>

## <a name="configure-the-httpmessagehandler"></a><span data-ttu-id="f9e53-505">HttpMessageHandler 구성</span><span class="sxs-lookup"><span data-stu-id="f9e53-505">Configure the HttpMessageHandler</span></span>

<span data-ttu-id="f9e53-506">클라이언트가 사용하는 내부 `HttpMessageHandler`의 구성을 제어해야 할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-506">It may be necessary to control the configuration of the inner `HttpMessageHandler` used by a client.</span></span>

<span data-ttu-id="f9e53-507">`IHttpClientBuilder`는 명명된 또는 형식화된 클라이언트를 추가할 때 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-507">An `IHttpClientBuilder` is returned when adding named or typed clients.</span></span> <span data-ttu-id="f9e53-508"><xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> 확장 메서드는 대리자를 정의하는 데 사용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-508">The <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> extension method can be used to define a delegate.</span></span> <span data-ttu-id="f9e53-509">대리자는 해당 클라이언트가 사용하는 기본 `HttpMessageHandler`을 만들고 구성하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-509">The delegate is used to create and configure the primary `HttpMessageHandler` used by that client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet12)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a><span data-ttu-id="f9e53-510">콘솔 앱에서 IHttpClientFactory 사용</span><span class="sxs-lookup"><span data-stu-id="f9e53-510">Use IHttpClientFactory in a console app</span></span>

<span data-ttu-id="f9e53-511">콘솔 앱에서 프로젝트에 다음 패키지 참조를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-511">In a console app, add the following package references to the project:</span></span>

* [<span data-ttu-id="f9e53-512">Microsoft.Extensions.Hosting</span><span class="sxs-lookup"><span data-stu-id="f9e53-512">Microsoft.Extensions.Hosting</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [<span data-ttu-id="f9e53-513">Microsoft.Extensions.Http</span><span class="sxs-lookup"><span data-stu-id="f9e53-513">Microsoft.Extensions.Http</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Http)

<span data-ttu-id="f9e53-514">다음 예제에서는</span><span class="sxs-lookup"><span data-stu-id="f9e53-514">In the following example:</span></span>

* <span data-ttu-id="f9e53-515"><xref:System.Net.Http.IHttpClientFactory>는 [제너릭 호스트의](xref:fundamentals/host/generic-host) 서비스 컨테이너에 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-515"><xref:System.Net.Http.IHttpClientFactory> is registered in the [Generic Host's](xref:fundamentals/host/generic-host) service container.</span></span>
* <span data-ttu-id="f9e53-516">`MyService`에서는 `HttpClient`를 만드는 데 사용하는 서비스에서 클라이언트 팩터리 인스턴스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-516">`MyService` creates a client factory instance from the service, which is used to create an `HttpClient`.</span></span> <span data-ttu-id="f9e53-517">`HttpClient`는 웹 페이지를 검색하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-517">`HttpClient` is used to retrieve a webpage.</span></span>
* <span data-ttu-id="f9e53-518">`Main`을 통해서는 서비스의 `GetPage` 메서드를 실행하고 웹 페이지 콘텐츠의 처음 500자를 콘솔에 씁니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-518">`Main` creates a scope to execute the service's `GetPage` method and write the first 500 characters of the webpage content to the console.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="additional-resources"></a><span data-ttu-id="f9e53-519">추가 자료</span><span class="sxs-lookup"><span data-stu-id="f9e53-519">Additional resources</span></span>

* [<span data-ttu-id="f9e53-520">HttpClientFactory를 사용하여 복원력 있는 HTTP 요청 구현</span><span class="sxs-lookup"><span data-stu-id="f9e53-520">Use HttpClientFactory to implement resilient HTTP requests</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [<span data-ttu-id="f9e53-521">HttpClientFactory 및 Polly 정책을 통해 지수 백오프를 사용하여 HTTP 호출 다시 시도 구현</span><span class="sxs-lookup"><span data-stu-id="f9e53-521">Implement HTTP call retries with exponential backoff with HttpClientFactory and Polly policies</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [<span data-ttu-id="f9e53-522">회로 차단기 패턴 구현</span><span class="sxs-lookup"><span data-stu-id="f9e53-522">Implement the Circuit Breaker pattern</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="f9e53-523">작성자: [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak) 및 [Steve Gordon](https://github.com/stevejgordon)</span><span class="sxs-lookup"><span data-stu-id="f9e53-523">By [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak), and [Steve Gordon](https://github.com/stevejgordon)</span></span>

<span data-ttu-id="f9e53-524">앱에서 <xref:System.Net.Http.IHttpClientFactory>를 등록하여 <xref:System.Net.Http.HttpClient> 인스턴스를 구성하고 만드는 데 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-524">An <xref:System.Net.Http.IHttpClientFactory> can be registered and used to configure and create <xref:System.Net.Http.HttpClient> instances in an app.</span></span> <span data-ttu-id="f9e53-525">이는 다음과 같은 이점을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-525">It offers the following benefits:</span></span>

* <span data-ttu-id="f9e53-526">논리적 `HttpClient` 인스턴스를 구성하고 이름을 지정하기 위한 중앙 위치를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-526">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="f9e53-527">예를 들어, [GitHub](https://github.com/)에 액세스하는 *github* 클라이언트를 등록 및 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-527">For example, a *github* client can be registered and configured to access [GitHub](https://github.com/).</span></span> <span data-ttu-id="f9e53-528">기본 클라이언트는 다른 용도로 등록할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-528">A default client can be registered for other purposes.</span></span>
* <span data-ttu-id="f9e53-529">`HttpClient`에서 위임 처리기를 통해 나가는 미들웨어의 개념을 체계화하고 Polly 기반 미들웨어에 대한 확장을 제공하여 이를 활용합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-529">Codifies the concept of outgoing middleware via delegating handlers in `HttpClient` and provides extensions for Polly-based middleware to take advantage of that.</span></span>
* <span data-ttu-id="f9e53-530">`HttpClient` 수명을 수동으로 관리할 때 발생하는 일반적인 DNS 문제를 피하기 위해 기본 `HttpClientMessageHandler` 인스턴스의 풀링 및 수명을 관리합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-530">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances to avoid common DNS problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="f9e53-531">팩터리에서 만든 클라이언트를 통해 전송된 모든 요청에 대해 구성 가능한 로깅 경험(`ILogger`을 통해)을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-531">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="f9e53-532">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="f9e53-532">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="f9e53-533">사전 요구 사항</span><span class="sxs-lookup"><span data-stu-id="f9e53-533">Prerequisites</span></span>

<span data-ttu-id="f9e53-534">.NET Framework를 대상으로 하는 프로젝트는 [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) NuGet 패키지를 설치해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-534">Projects targeting .NET Framework require installation of the [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) NuGet package.</span></span> <span data-ttu-id="f9e53-535">.NET Core를 대상으로 하고 [Microsoft.AspNetCore.App 메타패키지](xref:fundamentals/metapackage-app)를 참조하는 프로젝트는 이미 `Microsoft.Extensions.Http` 패키지를 포함하고 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-535">Projects that target .NET Core and reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) already include the `Microsoft.Extensions.Http` package.</span></span>

## <a name="consumption-patterns"></a><span data-ttu-id="f9e53-536">사용 패턴</span><span class="sxs-lookup"><span data-stu-id="f9e53-536">Consumption patterns</span></span>

<span data-ttu-id="f9e53-537">앱에서 `IHttpClientFactory`를 사용할 수 있는 몇 가지 방법이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-537">There are several ways `IHttpClientFactory` can be used in an app:</span></span>

* [<span data-ttu-id="f9e53-538">기본적인 사용 방법</span><span class="sxs-lookup"><span data-stu-id="f9e53-538">Basic usage</span></span>](#basic-usage)
* [<span data-ttu-id="f9e53-539">명명된 클라이언트</span><span class="sxs-lookup"><span data-stu-id="f9e53-539">Named clients</span></span>](#named-clients)
* [<span data-ttu-id="f9e53-540">형식화된 클라이언트</span><span class="sxs-lookup"><span data-stu-id="f9e53-540">Typed clients</span></span>](#typed-clients)
* [<span data-ttu-id="f9e53-541">생성된 클라이언트</span><span class="sxs-lookup"><span data-stu-id="f9e53-541">Generated clients</span></span>](#generated-clients)

<span data-ttu-id="f9e53-542">어떤 방법도 다른 방법에 비해 절대적으로 우수하지는 않습니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-542">None of them are strictly superior to another.</span></span> <span data-ttu-id="f9e53-543">가장 좋은 방법은 앱의 제약 조건에 따라서 달라집니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-543">The best approach depends upon the app's constraints.</span></span>

### <a name="basic-usage"></a><span data-ttu-id="f9e53-544">기본적인 사용 방법</span><span class="sxs-lookup"><span data-stu-id="f9e53-544">Basic usage</span></span>

<span data-ttu-id="f9e53-545">`IHttpClientFactory`는 `Startup.ConfigureServices` 메서드 내에서 `IServiceCollection`의 `AddHttpClient` 확장 메서드를 호출하여 등록할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-545">The `IHttpClientFactory` can be registered by calling the `AddHttpClient` extension method on the `IServiceCollection`, inside the `Startup.ConfigureServices` method.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

<span data-ttu-id="f9e53-546">일단 등록하고 나면 코드는 [DI(종속성 주입)](xref:fundamentals/dependency-injection)를 사용하여 서비스를 주입할 수 있는 모든 곳에서 `IHttpClientFactory`를 받을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-546">Once registered, code can accept an `IHttpClientFactory` anywhere services can be injected with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="f9e53-547">`IHttpClientFactory`을(를) 사용하여 `HttpClient` 인스턴스를 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-547">The `IHttpClientFactory` can be used to create an `HttpClient` instance:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

<span data-ttu-id="f9e53-548">이러한 방식으로 `IHttpClientFactory`를 사용하는 것은 기존 앱을 리팩터링할 수 있는 좋은 방법입니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-548">Using `IHttpClientFactory` in this fashion is a good way to refactor an existing app.</span></span> <span data-ttu-id="f9e53-549">`HttpClient`가 사용되는 방식에는 어떠한 영향도 없습니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-549">It has no impact on the way `HttpClient` is used.</span></span> <span data-ttu-id="f9e53-550">`HttpClient` 인스턴스가 현재 만들어지는 위치에서 해당 코드를 <xref:System.Net.Http.IHttpClientFactory.CreateClient*>에 대한 호출로 대체합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-550">In places where `HttpClient` instances are currently created, replace those occurrences with a call to <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span></span>

### <a name="named-clients"></a><span data-ttu-id="f9e53-551">명명된 클라이언트</span><span class="sxs-lookup"><span data-stu-id="f9e53-551">Named clients</span></span>

<span data-ttu-id="f9e53-552">앱이 각각 다른 구성을 사용하는 많은 개별적인 `HttpClient`의 사용을 필요로 하는 경우 선택할 수 있는 방법은 **명명된 클라이언트**를 사용하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-552">If an app requires many distinct uses of `HttpClient`, each with a different configuration, an option is to use **named clients**.</span></span> <span data-ttu-id="f9e53-553">명명된 `HttpClient`에 대한 구성은 `Startup.ConfigureServices`에서 등록하는 동안 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-553">Configuration for a named `HttpClient` can be specified during registration in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

<span data-ttu-id="f9e53-554">위의 코드에서는 `AddHttpClient`를 호출하여 *github*이라는 이름을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-554">In the preceding code, `AddHttpClient` is called, providing the name *github*.</span></span> <span data-ttu-id="f9e53-555">이 클라이언트에는 일부 기본 구성&mdash;즉 GitHub API를 작동하는 데 필요한 기준 주소 및 두 개의 헤더가 적용되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-555">This client has some default configuration applied&mdash;namely the base address and two headers required to work with the GitHub API.</span></span>

<span data-ttu-id="f9e53-556">`CreateClient`를 호출할 때마다 `HttpClient`의 새 인스턴스를 만들고 구성 작업을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-556">Each time `CreateClient` is called, a new instance of `HttpClient` is created and the configuration action is called.</span></span>

<span data-ttu-id="f9e53-557">명명된 클라이언트를 사용하기 위해서 `CreateClient`에 문자열 매개 변수를 전달할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-557">To consume a named client, a string parameter can be passed to `CreateClient`.</span></span> <span data-ttu-id="f9e53-558">만들 클라이언트의 이름을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-558">Specify the name of the client to be created:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

<span data-ttu-id="f9e53-559">위의 코드에서는 요청이 호스트 이름을 지정할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-559">In the preceding code, the request doesn't need to specify a hostname.</span></span> <span data-ttu-id="f9e53-560">클라이언트에 대해 구성된 기본 주소를 사용하므로 경로만 전달할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-560">It can pass just the path, since the base address configured for the client is used.</span></span>

### <a name="typed-clients"></a><span data-ttu-id="f9e53-561">형식화된 클라이언트</span><span class="sxs-lookup"><span data-stu-id="f9e53-561">Typed clients</span></span>

<span data-ttu-id="f9e53-562">형식화된 클라이언트:</span><span class="sxs-lookup"><span data-stu-id="f9e53-562">Typed clients:</span></span>

* <span data-ttu-id="f9e53-563">문자열을 키로 사용할 필요가 없이 명명된 클라이언트와 동일한 기능을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-563">Provide the same capabilities as named clients without the need to use strings as keys.</span></span>
* <span data-ttu-id="f9e53-564">클라이언트를 사용할 때 IntelliSense 및 컴파일러 도움말을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-564">Provides IntelliSense and compiler help when consuming clients.</span></span>
* <span data-ttu-id="f9e53-565">특정 `HttpClient`을 구성하고 상호 작용하기 위해 단일 위치를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-565">Provide a single location to configure and interact with a particular `HttpClient`.</span></span> <span data-ttu-id="f9e53-566">예를 들어 단일 형식화된 클라이언트는 단일 백 엔드 엔드포인트에 사용될 수 있으며 해당 엔드포인트를 다루는 모든 논리를 캡슐화할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-566">For example, a single typed client might be used for a single backend endpoint and encapsulate all logic dealing with that endpoint.</span></span>
* <span data-ttu-id="f9e53-567">DI로 작업하고 앱에서 필요할 경우 삽입할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-567">Work with DI and can be injected where required in your app.</span></span>

<span data-ttu-id="f9e53-568">형식화된 클라이언트는 생성자에서 `HttpClient` 매개 변수를 받습니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-568">A typed client accepts an `HttpClient` parameter in its constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]

<span data-ttu-id="f9e53-569">위의 코드에서는 구성이 형식화된 클라이언트로 이동되었습니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-569">In the preceding code, the configuration is moved into the typed client.</span></span> <span data-ttu-id="f9e53-570">`HttpClient` 개체는 공용 속성으로 노출됩니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-570">The `HttpClient` object is exposed as a public property.</span></span> <span data-ttu-id="f9e53-571">`HttpClient` 기능을 노출하는 API 특정 메서드를 정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-571">It's possible to define API-specific methods that expose `HttpClient` functionality.</span></span> <span data-ttu-id="f9e53-572">`GetAspNetDocsIssues` 메서드는 GitHub 리포지토리에서 공개된 최신 문제를 구문 분석하고 쿼리하는 데 필요한 코드를 캡슐화합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-572">The `GetAspNetDocsIssues` method encapsulates the code needed to query for and parse out the latest open issues from a GitHub repository.</span></span>

<span data-ttu-id="f9e53-573">형식화된 클라이언트를 등록하기 위해서 `Startup.ConfigureServices`에서 형식화된 클라이언트 클래스를 지정하여 제네릭 <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> 확장 메서드를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-573">To register a typed client, the generic <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> extension method can be used within `Startup.ConfigureServices`, specifying the typed client class:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

<span data-ttu-id="f9e53-574">형식화된 클라이언트는 DI를 사용하여 일시적으로 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-574">The typed client is registered as transient with DI.</span></span> <span data-ttu-id="f9e53-575">형식화된 클라이언트는 직접 주입되고 사용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-575">The typed client can be injected and consumed directly:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

<span data-ttu-id="f9e53-576">원한다면 형식화된 클라이언트의 생성자 대신 `Startup.ConfigureServices`에서 등록하는 동안 형식화된 클라이언트에 대한 구성을 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-576">If preferred, the configuration for a typed client can be specified during registration in `Startup.ConfigureServices`, rather than in the typed client's constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

<span data-ttu-id="f9e53-577">형식화된 클라이언트 내에서 `HttpClient`를 완전히 캡슐화할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-577">It's possible to entirely encapsulate the `HttpClient` within a typed client.</span></span> <span data-ttu-id="f9e53-578">속성으로 노출하는 대신 공용 메서드를 제공하여 내부적으로 `HttpClient` 인스턴스를 호출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-578">Rather than exposing it as a property, public methods can be provided which call the `HttpClient` instance internally.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

<span data-ttu-id="f9e53-579">위의 코드에서는 `HttpClient`가 전용 필드로 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-579">In the preceding code, the `HttpClient` is stored as a private field.</span></span> <span data-ttu-id="f9e53-580">외부 호출을 하기 위한 모든 액세스는 `GetRepos` 메서드를 거칩니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-580">All access to make external calls goes through the `GetRepos` method.</span></span>

### <a name="generated-clients"></a><span data-ttu-id="f9e53-581">생성된 클라이언트</span><span class="sxs-lookup"><span data-stu-id="f9e53-581">Generated clients</span></span>

<span data-ttu-id="f9e53-582">`IHttpClientFactory`는 [Refit](https://github.com/paulcbetts/refit)과 같은 다른 타사 라이브러리와 함께 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-582">`IHttpClientFactory` can be used in combination with other third-party libraries such as [Refit](https://github.com/paulcbetts/refit).</span></span> <span data-ttu-id="f9e53-583">Refit은 .NET용 REST 라이브러리입니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-583">Refit is a REST library for .NET.</span></span> <span data-ttu-id="f9e53-584">REST API를 라이브 인터페이스로 변환합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-584">It converts REST APIs into live interfaces.</span></span> <span data-ttu-id="f9e53-585">인터페이스의 구현은 `HttpClient`를 사용하여 외부 HTTP를 호출하도록 `RestService`에 의해 동적으로 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-585">An implementation of the interface is generated dynamically by the `RestService`, using `HttpClient` to make the external HTTP calls.</span></span>

<span data-ttu-id="f9e53-586">외부 API와 해당 응답을 나타내기 위한 인터페이스와 회신이 정의됩니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-586">An interface and a reply are defined to represent the external API and its response:</span></span>

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

<span data-ttu-id="f9e53-587">구현을 생성하기 위해 Refit를 사용하여 형식화된 클라이언트를 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-587">A typed client can be added, using Refit to generate the implementation:</span></span>

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

<span data-ttu-id="f9e53-588">DI 및 Refit에서 제공한 구현을 통해 필요한 곳에서 정의된 인터페이스를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-588">The defined interface can be consumed where necessary, with the implementation provided by DI and Refit:</span></span>

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

## <a name="outgoing-request-middleware"></a><span data-ttu-id="f9e53-589">나가는 요청 미들웨어</span><span class="sxs-lookup"><span data-stu-id="f9e53-589">Outgoing request middleware</span></span>

<span data-ttu-id="f9e53-590">`HttpClient`에는 나가는 HTTP 요청을 위해 함께 연결될 수 있는 위임 처리기라는 개념이 이미 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-590">`HttpClient` already has the concept of delegating handlers that can be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="f9e53-591">`IHttpClientFactory`를 사용하면 각 명명된 클라이언트에 적용할 처리기를 쉽게 정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-591">The `IHttpClientFactory` makes it easy to define the handlers to apply for each named client.</span></span> <span data-ttu-id="f9e53-592">나가는 요청 미들웨어 파이프라인을 만들기 위한 여러 처리기의 등록 및 연결을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-592">It supports registration and chaining of multiple handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="f9e53-593">이러한 처리기 각각은 나가는 요청 전후에 작업을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-593">Each of these handlers is able to perform work before and after the outgoing request.</span></span> <span data-ttu-id="f9e53-594">이 패턴은 ASP.NET Core의 인바운드 미들웨어 파이프라인과 비슷합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-594">This pattern is similar to the inbound middleware pipeline in ASP.NET Core.</span></span> <span data-ttu-id="f9e53-595">이 패턴은 캐싱, 오류 처리, 직렬화 및 로깅을 포함한 HTTP 요청을 둘러싼 횡단 관심사를 관리하기 위한 메커니즘을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-595">The pattern provides a mechanism to manage cross-cutting concerns around HTTP requests, including caching, error handling, serialization, and logging.</span></span>

<span data-ttu-id="f9e53-596">처리기를 만들려면 <xref:System.Net.Http.DelegatingHandler>에서 파생되는 클래스를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-596">To create a handler, define a class deriving from <xref:System.Net.Http.DelegatingHandler>.</span></span> <span data-ttu-id="f9e53-597">파이프라인의 다음 처리기로 요청을 전달하기 전에 코드를 실행하려면 `SendAsync` 메서드를 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-597">Override the `SendAsync` method to execute code before passing the request to the next handler in the pipeline:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

<span data-ttu-id="f9e53-598">위의 코드에서는 기본 처리기를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-598">The preceding code defines a basic handler.</span></span> <span data-ttu-id="f9e53-599">`X-API-KEY` 헤더가 요청에 포함되었는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-599">It checks to see if an `X-API-KEY` header has been included on the request.</span></span> <span data-ttu-id="f9e53-600">헤더가 누락된 경우 HTTP 호출을 방지하고 적합한 응답을 반환할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-600">If the header is missing, it can avoid the HTTP call and return a suitable response.</span></span>

<span data-ttu-id="f9e53-601">등록하는 동안 하나 이상의 처리기를 `HttpClient`의 구성에 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-601">During registration, one or more handlers can be added to the configuration for an `HttpClient`.</span></span> <span data-ttu-id="f9e53-602">이 작업은 <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>의 확장 메서드를 통해 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-602">This task is accomplished via extension methods on the <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet5)]

<span data-ttu-id="f9e53-603">위의 코드에서 `ValidateHeaderHandler`는 DI에 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-603">In the preceding code, the `ValidateHeaderHandler` is registered with DI.</span></span> <span data-ttu-id="f9e53-604">처리기는 범위가 지정되지 않은, 임시 서비스로 DI에 등록**되어야** 합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-604">The handler **must** be registered in DI as a transient service, never scoped.</span></span> <span data-ttu-id="f9e53-605">처리기가 범위 지정 서비스로 등록되고 처리기가 종속된 모든 서비스는 삭제 가능합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-605">If the handler is registered as a scoped service and any services that the handler depends upon are disposable:</span></span>

* <span data-ttu-id="f9e53-606">처리기가 범위를 벗어나기 전에 처리기의 서비스를 삭제할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-606">The handler's services could be disposed before the handler goes out of scope.</span></span>
* <span data-ttu-id="f9e53-607">처리기 서비스를 삭제하면 처리기가 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-607">The disposed handler services causes the handler to fail.</span></span>

<span data-ttu-id="f9e53-608">일단 등록되면 처리기 형식으로 전달하여 <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*>를 호출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-608">Once registered, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> can be called, passing in the handler type.</span></span>

<span data-ttu-id="f9e53-609">실행해야 하는 순서에 따라 여러 처리기를 등록할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-609">Multiple handlers can be registered in the order that they should execute.</span></span> <span data-ttu-id="f9e53-610">각 처리기는 최종 `HttpClientHandler`가 요청을 실행할 때까지 다음 처리기를 래핑합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-610">Each handler wraps the next handler until the final `HttpClientHandler` executes the request:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

<span data-ttu-id="f9e53-611">다음 방법 중 하나를 사용하여 메시지 처리기와 요청별 상태를 공유하세요.</span><span class="sxs-lookup"><span data-stu-id="f9e53-611">Use one of the following approaches to share per-request state with message handlers:</span></span>

* <span data-ttu-id="f9e53-612">`HttpRequestMessage.Properties`를 사용하여 데이터를 처리기로 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-612">Pass data into the handler using `HttpRequestMessage.Properties`.</span></span>
* <span data-ttu-id="f9e53-613">`IHttpContextAccessor`를 사용하여 현재 요청에 액세스합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-613">Use `IHttpContextAccessor` to access the current request.</span></span>
* <span data-ttu-id="f9e53-614">사용자 지정 `AsyncLocal` 스토리지 개체를 만들어 데이터를 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-614">Create a custom `AsyncLocal` storage object to pass the data.</span></span>

## <a name="use-polly-based-handlers"></a><span data-ttu-id="f9e53-615">Polly 기반 처리기 사용</span><span class="sxs-lookup"><span data-stu-id="f9e53-615">Use Polly-based handlers</span></span>

<span data-ttu-id="f9e53-616">`IHttpClientFactory`는 [Polly](https://github.com/App-vNext/Polly)라는 유명한 타사 라이브러리와 통합됩니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-616">`IHttpClientFactory` integrates with a popular third-party library called [Polly](https://github.com/App-vNext/Polly).</span></span> <span data-ttu-id="f9e53-617">Polly는 .NET용 포괄적인 회복탄력성 및 일시적 오류 처리 라이브러리입니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-617">Polly is a comprehensive resilience and transient fault-handling library for .NET.</span></span> <span data-ttu-id="f9e53-618">개발자는 이를 사용하여 재시도, 회로 차단기, 시간 초과, 격벽 격리 및 대체(Fallback) 같은 정책을 유연하고 스레드로부터 안전한 방식으로 표현할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-618">It allows developers to express policies such as Retry, Circuit Breaker, Timeout, Bulkhead Isolation, and Fallback in a fluent and thread-safe manner.</span></span>

<span data-ttu-id="f9e53-619">구성된 `HttpClient` 인스턴스를 통해 Polly 정책을 사용할 수 있도록 확장 메서드가 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-619">Extension methods are provided to enable the use of Polly policies with configured `HttpClient` instances.</span></span> <span data-ttu-id="f9e53-620">Polly 확장은:</span><span class="sxs-lookup"><span data-stu-id="f9e53-620">The Polly extensions:</span></span>

* <span data-ttu-id="f9e53-621">클라이언트에 Polly 기반 처리기 추가를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-621">Support adding Polly-based handlers to clients.</span></span>
* <span data-ttu-id="f9e53-622">[Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet 패키지를 설치한 후 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-622">Can be used after installing the [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet package.</span></span> <span data-ttu-id="f9e53-623">이 패키지는 ASP.NET Core 공유 프레임워크에 포함되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-623">The package isn't included in the ASP.NET Core shared framework.</span></span>

### <a name="handle-transient-faults"></a><span data-ttu-id="f9e53-624">일시적인 오류 처리</span><span class="sxs-lookup"><span data-stu-id="f9e53-624">Handle transient faults</span></span>

<span data-ttu-id="f9e53-625">가장 일반적인 오류는 외부 HTTP 호출이 일시적인 경우 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-625">Most common faults occur when external HTTP calls are transient.</span></span> <span data-ttu-id="f9e53-626">일시적인 오류를 처리하기 위한 정책을 정의할 수 있는 `AddTransientHttpErrorPolicy`라는 편리한 확장 메서드가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-626">A convenient extension method called `AddTransientHttpErrorPolicy` is included which allows a policy to be defined to handle transient errors.</span></span> <span data-ttu-id="f9e53-627">이 확장 메서드로 구성된 정책은 `HttpRequestException`, HTTP 5xx 응답 및 HTTP 408 응답을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-627">Policies configured with this extension method handle `HttpRequestException`, HTTP 5xx responses, and HTTP 408 responses.</span></span>

<span data-ttu-id="f9e53-628">`AddTransientHttpErrorPolicy` 확장은 `Startup.ConfigureServices` 내에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-628">The `AddTransientHttpErrorPolicy` extension can be used within `Startup.ConfigureServices`.</span></span> <span data-ttu-id="f9e53-629">이 확장은 가능한 일시적 오류를 나타내는 오류를 처리하기 위해 구성된 `PolicyBuilder` 개체에 대한 액세스를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-629">The extension provides access to a `PolicyBuilder` object configured to handle errors representing a possible transient fault:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet7)]

<span data-ttu-id="f9e53-630">위의 코드에서는 `WaitAndRetryAsync` 정책을 정의하고 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-630">In the preceding code, a `WaitAndRetryAsync` policy is defined.</span></span> <span data-ttu-id="f9e53-631">실패한 요청은 최대 세 번까지 다시 시도되며 시도 간 600밀리초의 지연 간격을 둡니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-631">Failed requests are retried up to three times with a delay of 600 ms between attempts.</span></span>

### <a name="dynamically-select-policies"></a><span data-ttu-id="f9e53-632">동적으로 정책 선택</span><span class="sxs-lookup"><span data-stu-id="f9e53-632">Dynamically select policies</span></span>

<span data-ttu-id="f9e53-633">Polly 기반 처리기를 추가하는 데 사용할 수 있는 추가 확장 메서드가 존재합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-633">Additional extension methods exist which can be used to add Polly-based handlers.</span></span> <span data-ttu-id="f9e53-634">이러한 확장 중 하나는 여러 오버로드가 있는 `AddPolicyHandler`입니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-634">One such extension is `AddPolicyHandler`, which has multiple overloads.</span></span> <span data-ttu-id="f9e53-635">한 오버로드는 적용할 정책을 정의할 때 요청을 검사할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-635">One overload allows the request to be inspected when defining which policy to apply:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

<span data-ttu-id="f9e53-636">위의 코드에서는 나가는 요청이 HTTP GET인 경우 10초 시간 제한이 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-636">In the preceding code, if the outgoing request is an HTTP GET, a 10-second timeout is applied.</span></span> <span data-ttu-id="f9e53-637">다른 HTTP 메서드의 경우 30초 시간 제한이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-637">For any other HTTP method, a 30-second timeout is used.</span></span>

### <a name="add-multiple-polly-handlers"></a><span data-ttu-id="f9e53-638">여러 Polly 처리기 추가</span><span class="sxs-lookup"><span data-stu-id="f9e53-638">Add multiple Polly handlers</span></span>

<span data-ttu-id="f9e53-639">향상된 기능을 제공하기 위해 Polly 정책을 중첩하는 것이 일반적입니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-639">It's common to nest Polly policies to provide enhanced functionality:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

<span data-ttu-id="f9e53-640">위의 예제에서는 두 개의 처리기가 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-640">In the preceding example, two handlers are added.</span></span> <span data-ttu-id="f9e53-641">첫 번째 처리기는 재시도 정책을 추가하기 위해 `AddTransientHttpErrorPolicy` 확장을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-641">The first uses the `AddTransientHttpErrorPolicy` extension to add a retry policy.</span></span> <span data-ttu-id="f9e53-642">실패한 요청은 최대 세 번까지 다시 시도됩니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-642">Failed requests are retried up to three times.</span></span> <span data-ttu-id="f9e53-643">`AddTransientHttpErrorPolicy`에 대한 두 번째 호출은 회로 차단기 정책을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-643">The second call to `AddTransientHttpErrorPolicy` adds a circuit breaker policy.</span></span> <span data-ttu-id="f9e53-644">5번의 시도가 순차적으로 실패하는 경우 추가적인 외부 요청은 30초 동안 차단됩니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-644">Further external requests are blocked for 30 seconds if five failed attempts occur sequentially.</span></span> <span data-ttu-id="f9e53-645">회로 차단기 정책은 상태를 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-645">Circuit breaker policies are stateful.</span></span> <span data-ttu-id="f9e53-646">이 클라이언트를 통한 모든 호출은 동일한 회로 상태를 공유합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-646">All calls through this client share the same circuit state.</span></span>

### <a name="add-policies-from-the-polly-registry"></a><span data-ttu-id="f9e53-647">Polly 레지스트리로부터 정책 추가</span><span class="sxs-lookup"><span data-stu-id="f9e53-647">Add policies from the Polly registry</span></span>

<span data-ttu-id="f9e53-648">정기적으로 사용되는 정책을 관리하는 방법은 정책을 한 번 정의하고 이를 `PolicyRegistry`에 등록하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-648">An approach to managing regularly used policies is to define them once and register them with a `PolicyRegistry`.</span></span> <span data-ttu-id="f9e53-649">레지스트리의 정책을 사용하여 처리기를 추가할 수 있는 확장 메서드가 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-649">An extension method is provided which allows a handler to be added using a policy from the registry:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet10)]

<span data-ttu-id="f9e53-650">위의 코드에서는 `PolicyRegistry`가 `ServiceCollection`에 추가될 때 두 가지 정책이 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-650">In the preceding code, two policies are registered when the `PolicyRegistry` is added to the `ServiceCollection`.</span></span> <span data-ttu-id="f9e53-651">레지스트리의 정책을 사용하기 위해서는 적용할 정책의 이름을 전달하여 `AddPolicyHandlerFromRegistry` 메서드를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-651">To use a policy from the registry, the `AddPolicyHandlerFromRegistry` method is used, passing the name of the policy to apply.</span></span>

<span data-ttu-id="f9e53-652">`IHttpClientFactory` 및 Polly 통합에 대한 추가 정보는 [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory)에서 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-652">Further information about `IHttpClientFactory` and Polly integrations can be found on the [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span></span>

## <a name="httpclient-and-lifetime-management"></a><span data-ttu-id="f9e53-653">HttpClient 및 수명 관리</span><span class="sxs-lookup"><span data-stu-id="f9e53-653">HttpClient and lifetime management</span></span>

<span data-ttu-id="f9e53-654">`IHttpClientFactory`에서 `CreateClient`가 호출될 때마다 새로운 `HttpClient` 인스턴스가 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-654">A new `HttpClient` instance is returned each time `CreateClient` is called on the `IHttpClientFactory`.</span></span> <span data-ttu-id="f9e53-655">명명된 클라이언트마다 <xref:System.Net.Http.HttpMessageHandler>가 존재합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-655">There's an <xref:System.Net.Http.HttpMessageHandler> per named client.</span></span> <span data-ttu-id="f9e53-656">팩터리는 `HttpMessageHandler` 인스턴스의 수명을 관리합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-656">The factory manages the lifetimes of the `HttpMessageHandler` instances.</span></span>

<span data-ttu-id="f9e53-657">`IHttpClientFactory`는 리소스 사용을 줄이기 위해 팩터리에서 만든 `HttpMessageHandler` 인스턴스를 풀링합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-657">`IHttpClientFactory` pools the `HttpMessageHandler` instances created by the factory to reduce resource consumption.</span></span> <span data-ttu-id="f9e53-658">수명이 만료되지 않은 경우, 새 `HttpClient` 인스턴스를 만들 때 풀에서 `HttpMessageHandler` 인스턴스가 재사용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-658">An `HttpMessageHandler` instance may be reused from the pool when creating a new `HttpClient` instance if its lifetime hasn't expired.</span></span>

<span data-ttu-id="f9e53-659">일반적으로 각 처리기는 자체적인 기본 HTTP 연결을 관리하므로 처리기의 풀링이 적합합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-659">Pooling of handlers is desirable as each handler typically manages its own underlying HTTP connections.</span></span> <span data-ttu-id="f9e53-660">필요한 것보다 많은 처리기를 만들면 연결 지연이 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-660">Creating more handlers than necessary can result in connection delays.</span></span> <span data-ttu-id="f9e53-661">또한 일부 처리기는 무한정으로 연결을 열어 놓아 처리기가 DNS 변경에 대응하는 것을 막을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-661">Some handlers also keep connections open indefinitely, which can prevent the handler from reacting to DNS changes.</span></span>

<span data-ttu-id="f9e53-662">기본 처리기 수명은 2분입니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-662">The default handler lifetime is two minutes.</span></span> <span data-ttu-id="f9e53-663">명명된 클라이언트별 기준으로 기본값을 재정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-663">The default value can be overridden on a per named client basis.</span></span> <span data-ttu-id="f9e53-664">이를 재정의하려면 클라이언트를 만들 때 반환되는 `IHttpClientBuilder`에서 <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*>을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-664">To override it, call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> on the `IHttpClientBuilder` that is returned when creating the client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet11)]

<span data-ttu-id="f9e53-665">클라이언트의 삭제는 불필요합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-665">Disposal of the client isn't required.</span></span> <span data-ttu-id="f9e53-666">삭제는 나가는 요청을 취소하고 <xref:System.IDisposable.Dispose*>를 호출한 후에는 지정된 `HttpClient` 인스턴스가 사용될 수 없도록 보장합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-666">Disposal cancels outgoing requests and guarantees the given `HttpClient` instance can't be used after calling <xref:System.IDisposable.Dispose*>.</span></span> <span data-ttu-id="f9e53-667">`IHttpClientFactory`는 `HttpClient` 인스턴스에서 사용되는 리소스를 추적하고 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-667">`IHttpClientFactory` tracks and disposes resources used by `HttpClient` instances.</span></span> <span data-ttu-id="f9e53-668">`HttpClient` 인스턴스는 일반적으로 삭제가 필요하지 않은 .NET 개체로 간주할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-668">The `HttpClient` instances can generally be treated as .NET objects not requiring disposal.</span></span>

<span data-ttu-id="f9e53-669">장기간 단일 `HttpClient` 인스턴스를 활성 상태로 유지하는 것은 `IHttpClientFactory`가 등장하기 전에 사용되던 일반적인 패턴입니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-669">Keeping a single `HttpClient` instance alive for a long duration is a common pattern used before the inception of `IHttpClientFactory`.</span></span> <span data-ttu-id="f9e53-670">이 패턴은 `IHttpClientFactory`로 마이그레이션한 이후에는 불필요합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-670">This pattern becomes unnecessary after migrating to `IHttpClientFactory`.</span></span>

### <a name="alternatives-to-ihttpclientfactory"></a><span data-ttu-id="f9e53-671">IHttpClientFactory의 대안</span><span class="sxs-lookup"><span data-stu-id="f9e53-671">Alternatives to IHttpClientFactory</span></span>

<span data-ttu-id="f9e53-672">DI 지원 앱에서 `IHttpClientFactory`을(를) 사용하면 다음이 방지됩니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-672">Using `IHttpClientFactory` in a DI-enabled app avoids:</span></span>

* <span data-ttu-id="f9e53-673">`HttpMessageHandler` 인스턴스를 풀링하여 리소스 소모 문제가 발생했습니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-673">Resource exhaustion problems by pooling `HttpMessageHandler` instances.</span></span>
* <span data-ttu-id="f9e53-674">정기적으로 `HttpMessageHandler` 인스턴스를 순환하여 오래된 DNS 문제가 발생했습니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-674">Stale DNS problems by cycling `HttpMessageHandler` instances at regular intervals.</span></span>

<span data-ttu-id="f9e53-675">수명이 긴 <xref:System.Net.Http.SocketsHttpHandler> 인스턴스를 사용하여 위의 문제를 해결하는 다른 방법이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-675">There are alternative ways to solve the preceding problems using a long-lived <xref:System.Net.Http.SocketsHttpHandler> instance.</span></span>

- <span data-ttu-id="f9e53-676">앱 시작 시 `SocketsHttpHandler`의 인스턴스를 만들고 앱 수명 동안 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-676">Create an instance of `SocketsHttpHandler` when the app starts and use it for the life of the app.</span></span>
- <span data-ttu-id="f9e53-677">DNS 새로 고침 시간에 따라 적절한 값으로 <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime>을(를) 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-677">Configure <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> to an appropriate value based on DNS refresh times.</span></span>
- <span data-ttu-id="f9e53-678">필요에 따라 `new HttpClient(handler, disposeHandler: false)`을(를) 사용하여 `HttpClient` 인스턴스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-678">Create `HttpClient` instances using `new HttpClient(handler, disposeHandler: false)` as needed.</span></span>

<span data-ttu-id="f9e53-679">위의 방법은 비슷한 방식으로 `IHttpClientFactory`에서 해결하는 리소스 관리 문제를 해결합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-679">The preceding approaches solve the resource management problems that `IHttpClientFactory` solves in a similar way.</span></span>

- <span data-ttu-id="f9e53-680">`SocketsHttpHandler`은(는) `HttpClient` 인스턴스 간에 연결을 공유합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-680">The `SocketsHttpHandler` shares connections across `HttpClient` instances.</span></span> <span data-ttu-id="f9e53-681">이와 같이 공유하면 소켓이 소모되지 않도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-681">This sharing prevents socket exhaustion.</span></span>
- <span data-ttu-id="f9e53-682">오래된 DNS 문제를 방지하기 위해 `SocketsHttpHandler`에서 `PooledConnectionLifetime`에 따라 연결을 순환합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-682">The `SocketsHttpHandler` cycles connections according to `PooledConnectionLifetime` to avoid stale DNS problems.</span></span>

### <a name="cookies"></a><span data-ttu-id="f9e53-683">쿠키</span><span class="sxs-lookup"><span data-stu-id="f9e53-683">Cookies</span></span>

<span data-ttu-id="f9e53-684">풀링된 `HttpMessageHandler` 인스턴스는 `CookieContainer` 개체를 공유합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-684">The pooled `HttpMessageHandler` instances results in `CookieContainer` objects being shared.</span></span> <span data-ttu-id="f9e53-685">예상치 못한 `CookieContainer` 개체 공유로 잘못된 코드가 발생하는 경우가 많습니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-685">Unanticipated `CookieContainer` object sharing often results in incorrect code.</span></span> <span data-ttu-id="f9e53-686">쿠키가 필요한 앱의 경우 다음 중 하나를 고려하세요.</span><span class="sxs-lookup"><span data-stu-id="f9e53-686">For apps that require cookies, consider either:</span></span>

 - <span data-ttu-id="f9e53-687">자동 쿠키 처리 사용 안 함</span><span class="sxs-lookup"><span data-stu-id="f9e53-687">Disabling automatic cookie handling</span></span>
 - <span data-ttu-id="f9e53-688">`IHttpClientFactory` 방지</span><span class="sxs-lookup"><span data-stu-id="f9e53-688">Avoiding `IHttpClientFactory`</span></span>

<span data-ttu-id="f9e53-689"><xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*>을(를) 호출하여 자동 쿠키 처리를 사용하지 않도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-689">Call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> to disable automatic cookie handling:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a><span data-ttu-id="f9e53-690">로깅</span><span class="sxs-lookup"><span data-stu-id="f9e53-690">Logging</span></span>

<span data-ttu-id="f9e53-691">`IHttpClientFactory`을 통해 만든 클라이언트는 모든 요청에 대한 로그 메시지를 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-691">Clients created via `IHttpClientFactory` record log messages for all requests.</span></span> <span data-ttu-id="f9e53-692">기본 로그 메시지를 보려면 로깅 구성에서 적절한 정보 수준을 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-692">Enable the appropriate information level in your logging configuration to see the default log messages.</span></span> <span data-ttu-id="f9e53-693">요청 헤더의 로깅 등과 같은 추가 로깅은 추적 수준에서만 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-693">Additional logging, such as the logging of request headers, is only included at trace level.</span></span>

<span data-ttu-id="f9e53-694">각 클라이언트에 사용되는 로그 범주는 클라이언트의 이름을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-694">The log category used for each client includes the name of the client.</span></span> <span data-ttu-id="f9e53-695">예를 들어, *MyNamedClient*라는 클라이언트는 `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`의 범주를 사용하여 메시지를 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-695">A client named *MyNamedClient*, for example, logs messages with a category of `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`.</span></span> <span data-ttu-id="f9e53-696">*LogicalHandler*라는 접미사가 붙은 메시지는 요청 처리기 파이프라인 외부에서 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-696">Messages suffixed with *LogicalHandler* occur outside the request handler pipeline.</span></span> <span data-ttu-id="f9e53-697">요청 시 파이프라인의 다른 모든 처리기에서 이를 처리하기 전에 메시지가 기록됩니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-697">On the request, messages are logged before any other handlers in the pipeline have processed it.</span></span> <span data-ttu-id="f9e53-698">응답 시 다른 모든 파이프라인 처리기가 응답을 받은 후에 메시지가 기록됩니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-698">On the response, messages are logged after any other pipeline handlers have received the response.</span></span>

<span data-ttu-id="f9e53-699">로깅은 요청 처리기 파이프라인 내부에서도 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-699">Logging also occurs inside the request handler pipeline.</span></span> <span data-ttu-id="f9e53-700">*MyNamedClient* 예제에서 해당 메시지는 로그 범주 `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`에 대해 기록됩니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-700">In the *MyNamedClient* example, those messages are logged against the log category `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`.</span></span> <span data-ttu-id="f9e53-701">요청의 경우 이는 요청이 네트워크에서 전송되기 직전 및 다른 모든 처리기가 실행된 후에 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-701">For the request, this occurs after all other handlers have run and immediately before the request is sent out on the network.</span></span> <span data-ttu-id="f9e53-702">응답 시 이 로깅은 처리기 파이프라인을 통해 응답이 다시 전달되기 전의 응답 상태를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-702">On the response, this logging includes the state of the response before it passes back through the handler pipeline.</span></span>

<span data-ttu-id="f9e53-703">파이프라인 외부 및 내부에서 로깅을 사용하도록 설정하면 다른 파이프라인 처리기가 수행한 변경 내용을 검사할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-703">Enabling logging outside and inside the pipeline enables inspection of the changes made by the other pipeline handlers.</span></span> <span data-ttu-id="f9e53-704">예를 들어 여기에는 요청 헤더 또는 응답 상태 코드에 대한 변경이 포함될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-704">This may include changes to request headers, for example, or to the response status code.</span></span>

<span data-ttu-id="f9e53-705">로그 범주에 클라이언트의 이름을 포함하면 필요한 경우 명명된 특정 클라이언트에 대한 로그 필터링을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-705">Including the name of the client in the log category enables log filtering for specific named clients where necessary.</span></span>

## <a name="configure-the-httpmessagehandler"></a><span data-ttu-id="f9e53-706">HttpMessageHandler 구성</span><span class="sxs-lookup"><span data-stu-id="f9e53-706">Configure the HttpMessageHandler</span></span>

<span data-ttu-id="f9e53-707">클라이언트가 사용하는 내부 `HttpMessageHandler`의 구성을 제어해야 할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-707">It may be necessary to control the configuration of the inner `HttpMessageHandler` used by a client.</span></span>

<span data-ttu-id="f9e53-708">`IHttpClientBuilder`는 명명된 또는 형식화된 클라이언트를 추가할 때 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-708">An `IHttpClientBuilder` is returned when adding named or typed clients.</span></span> <span data-ttu-id="f9e53-709"><xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> 확장 메서드는 대리자를 정의하는 데 사용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-709">The <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> extension method can be used to define a delegate.</span></span> <span data-ttu-id="f9e53-710">대리자는 해당 클라이언트가 사용하는 기본 `HttpMessageHandler`을 만들고 구성하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-710">The delegate is used to create and configure the primary `HttpMessageHandler` used by that client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet12)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a><span data-ttu-id="f9e53-711">콘솔 앱에서 IHttpClientFactory 사용</span><span class="sxs-lookup"><span data-stu-id="f9e53-711">Use IHttpClientFactory in a console app</span></span>

<span data-ttu-id="f9e53-712">콘솔 앱에서 프로젝트에 다음 패키지 참조를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-712">In a console app, add the following package references to the project:</span></span>

* [<span data-ttu-id="f9e53-713">Microsoft.Extensions.Hosting</span><span class="sxs-lookup"><span data-stu-id="f9e53-713">Microsoft.Extensions.Hosting</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [<span data-ttu-id="f9e53-714">Microsoft.Extensions.Http</span><span class="sxs-lookup"><span data-stu-id="f9e53-714">Microsoft.Extensions.Http</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Http)

<span data-ttu-id="f9e53-715">다음 예제에서는</span><span class="sxs-lookup"><span data-stu-id="f9e53-715">In the following example:</span></span>

* <span data-ttu-id="f9e53-716"><xref:System.Net.Http.IHttpClientFactory>는 [제너릭 호스트의](xref:fundamentals/host/generic-host) 서비스 컨테이너에 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-716"><xref:System.Net.Http.IHttpClientFactory> is registered in the [Generic Host's](xref:fundamentals/host/generic-host) service container.</span></span>
* <span data-ttu-id="f9e53-717">`MyService`에서는 `HttpClient`를 만드는 데 사용하는 서비스에서 클라이언트 팩터리 인스턴스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-717">`MyService` creates a client factory instance from the service, which is used to create an `HttpClient`.</span></span> <span data-ttu-id="f9e53-718">`HttpClient`는 웹 페이지를 검색하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-718">`HttpClient` is used to retrieve a webpage.</span></span>
* <span data-ttu-id="f9e53-719">`Main`을 통해서는 서비스의 `GetPage` 메서드를 실행하고 웹 페이지 콘텐츠의 처음 500자를 콘솔에 씁니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-719">`Main` creates a scope to execute the service's `GetPage` method and write the first 500 characters of the webpage content to the console.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="header-propagation-middleware"></a><span data-ttu-id="f9e53-720">헤더 전파 미들웨어</span><span class="sxs-lookup"><span data-stu-id="f9e53-720">Header propagation middleware</span></span>

<span data-ttu-id="f9e53-721">헤더 전파는 들어오는 요청에서 나가는 HTTP 클라이언트 요청으로 HTTP 헤더를 전파하는 커뮤니티 지원 미들웨어입니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-721">Header propagation is a community supported middleware to propagate HTTP headers from the incoming request to the outgoing HTTP Client requests.</span></span> <span data-ttu-id="f9e53-722">헤더 전파를 사용하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-722">To use header propagation:</span></span>

* <span data-ttu-id="f9e53-723">[HeaderPropagation](https://www.nuget.org/packages/HeaderPropagation) 패키지의 커뮤니티 지원 포트를 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-723">Reference the community supported port of the package [HeaderPropagation](https://www.nuget.org/packages/HeaderPropagation).</span></span> <span data-ttu-id="f9e53-724">ASP.NET Core 3.1 이상은 [Microsoft.AspNetCore.HeaderPropagation](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation)을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-724">ASP.NET Core 3.1 and later supports [Microsoft.AspNetCore.HeaderPropagation](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation).</span></span>

* <span data-ttu-id="f9e53-725">`Startup`에서 미들웨어 및 `HttpClient`를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-725">Configure the middleware and `HttpClient` in `Startup`:</span></span>

  [!code-csharp[](http-requests/samples/2.x/Startup21.cs?highlight=5-9,25&name=snippet)]

* <span data-ttu-id="f9e53-726">클라이언트는 아웃바운드 요청에 구성된 헤더를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="f9e53-726">The client includes the configured headers on outbound requests:</span></span>

  ```csharp
  var client = clientFactory.CreateClient("MyForwardingClient");
  var response = client.GetAsync(...);
  ```

## <a name="additional-resources"></a><span data-ttu-id="f9e53-727">추가 자료</span><span class="sxs-lookup"><span data-stu-id="f9e53-727">Additional resources</span></span>

* [<span data-ttu-id="f9e53-728">HttpClientFactory를 사용하여 복원력 있는 HTTP 요청 구현</span><span class="sxs-lookup"><span data-stu-id="f9e53-728">Use HttpClientFactory to implement resilient HTTP requests</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [<span data-ttu-id="f9e53-729">HttpClientFactory 및 Polly 정책을 통해 지수 백오프를 사용하여 HTTP 호출 다시 시도 구현</span><span class="sxs-lookup"><span data-stu-id="f9e53-729">Implement HTTP call retries with exponential backoff with HttpClientFactory and Polly policies</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [<span data-ttu-id="f9e53-730">회로 차단기 패턴 구현</span><span class="sxs-lookup"><span data-stu-id="f9e53-730">Implement the Circuit Breaker pattern</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)

::: moniker-end
