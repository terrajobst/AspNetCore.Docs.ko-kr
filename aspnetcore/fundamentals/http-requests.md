---
title: ASP.NET Core에서 IHttpClientFactory를 사용하여 HTTP 요청 만들기
author: stevejgordon
description: IHttpClientFactory 인터페이스를 사용하여 ASP.NET Core에서 논리적 HttpClient 인스턴스를 관리하는 방법에 대해 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 03/30/2019
uid: fundamentals/http-requests
ms.openlocfilehash: 270727443f091306ac3e4ce4e2ceb99b88bbc609
ms.sourcegitcommit: 78339e9891c8676db01a6e81e9cb0cdaa280162f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/17/2019
ms.locfileid: "58809210"
---
# <a name="make-http-requests-using-ihttpclientfactory-in-aspnet-core"></a>ASP.NET Core에서 IHttpClientFactory를 사용하여 HTTP 요청 만들기

작성자: [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak) 및 [Steve Gordon](https://github.com/stevejgordon)

앱에서 <xref:System.Net.Http.HttpClient> 인스턴스를 만들고 구성하려면 <xref:System.Net.Http.IHttpClientFactory>를 등록하고 사용할 수 있습니다. 다음과 같은 이점을 제공합니다.

* 논리적 `HttpClient` 인스턴스를 구성하고 이름을 지정하기 위해 중앙 위치를 제공합니다. 예를 들어, *github* 클라이언트는 GitHub에 액세스하도록 등록 및 구성할 수 있습니다. 다른 용도로 기본 클라이언트를 등록할 수 있습니다.
* `HttpClient`에서 처리기 위임을 통해 나가는 미들웨어의 개념을 체계화하고 Polly 기반 미들웨어에 대한 확장을 제공하여 이를 활용합니다.
* 풀링 및 기본의 수명을 관리 수동으로 `HttpClient` 수명을 관리하는 경우 발생하는 일반적인 DNS 문제를 방지하려면 기본 `HttpClientMessageHandler` 인스턴스의 수명 및 풀링을 관리합니다.
* 팩터리에서 만든 클라이언트를 통해 전송된 모든 요청에 대해 구성 가능한 로깅 환경(`ILogger`을 통해)을 추가합니다.

[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>전제 조건

.NET Framework를 대상으로 하는 프로젝트에는 [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) NuGet 패키지를 설치해야 합니다. .NET Core를 대상으로 하며 [Microsoft.AspNetCore.App 메타패키지](xref:fundamentals/metapackage-app)를 참조하는 프로젝트에는 `Microsoft.Extensions.Http` 패키지가 이미 포함되어 있습니다.

## <a name="consumption-patterns"></a>사용 패턴

앱에서 사용할 수 있는 여러 방법 `IHttpClientFactory`이 있습니다.

* [기본 사용](#basic-usage)
* [명명된 클라이언트](#named-clients)
* [형식화된 클라이언트](#typed-clients)
* [생성된 클라이언트](#generated-clients)

어느 것도 다른 것에 비해 월등히 우수하지 않습니다. 가장 좋은 방법은 앱의 제약 조건에 따라 다릅니다.

### <a name="basic-usage"></a>기본 사용

`IHttpClientFactory`는 `Startup.ConfigureServices` 메서드 내에서 `IServiceCollection`에 있는 `AddHttpClient` 확장 메서드를 호출하여 등록할 수 있습니다.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

일단 등록하면 코드는 서비스가 [DI(종속성 주입)](xref:fundamentals/dependency-injection)를 사용하여 주입할 수 있는 어느 곳에서나 `IHttpClientFactory`를 수락할 수 있습니다. `IHttpClientFactory`은 `HttpClient` 인스턴스를 만드는 데 사용할 수 있습니다.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

이러한 방식으로 `IHttpClientFactory`를 사용하는 것은 기존 앱을 리팩터링할 수 있는 좋은 방법입니다. `HttpClient`이 사용되는 방식에 아무 영향도 없습니다. 현재 `HttpClient` 인스턴스를 만드는 위치에서 이러한 발생을 <xref:System.Net.Http.IHttpClientFactory.CreateClient*>에 대한 호출로 바꿉니다.

### <a name="named-clients"></a>명명된 클라이언트

앱이 각각 구성이 다른 `HttpClient`의 다양한 사용을 요구하는 경우 옵션은 **명명된 클라이언트**를 사용하는 것입니다. 명명된 `HttpClient`에 대한 구성은 `Startup.ConfigureServices`에 등록하는 동안 지정할 수 있습니다.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

위의 코드에서는 `AddHttpClient`를 호출하여 *github* 이름을 제공합니다. 이 클라이언트에는 적용된 일부 기본 구성&mdash;즉 GitHub API를 작동하는 데 필요한 기준 주소 및 두 개의 헤더가 있습니다.

`CreateClient`을 호출할 때마다 `HttpClient`의 새 인스턴스를 만들고 구성 작업을 호출합니다.

명명된 클라이언트를 사용하려면 문자열 매개 변수는 `CreateClient`에 전달될 수 있습니다. 만들 클라이언트의 이름을 지정합니다.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

위의 코드에서는 요청이 호스트 이름을 지정할 필요가 없습니다. 클라이언트에 대해 구성된 기본 주소를 사용하므로 경로만 전달할 수 있습니다.

### <a name="typed-clients"></a>형식화된 클라이언트

형식화된 클라이언트는 문자열을 키로 사용할 필요가 없이 명명된 클라이언트와 동일한 기능을 제공 합니다. 형식화된 클라이언트 방법은 클라이언트를 사용할 때 IntelliSense 및 컴파일러 도움말을 제공합니다. 특정 `HttpClient`을 구성하고 상호 작용하기 위해 단일 위치를 제공합니다. 예를 들어 단일 형식의 클라이언트는 단일 백 엔드 엔드포인트에 사용될 수 있으며 해당 엔드포인트를 처리하는 모든 논리를 캡슐화할 수 있습니다. 다른 이점은 DI로 작업하고 앱에서 필요할 경우 삽입할 수 있다는 점입니다.

형식화된 클라이언트는 생성자의 `HttpClient` 매개 변수를 수락합니다.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]

위의 코드에서 구성은 형식화된 클라이언트로 이동합니다. `HttpClient` 개체는 공용 속성으로 공개됩니다. `HttpClient` 기능을 공개하는 API 관련 메서드를 정의할 수 있습니다. `GetAspNetDocsIssues` 메서드는 GitHub 리포지토리에서 공개된 최신 문제를 구문 분석하고 쿼리하는 데 필요한 코드를 캡슐화합니다.

형식화된 클라이언트를 등록하려면 <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> 확장 메서드는 형식화된 클라이언트 클래스를 지정하면서 `Startup.ConfigureServices` 내에서 사용할 수 있습니다.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

형식화된 클라이언트는 DI를 사용하여 일시적으로 등록됩니다. 형식화된 클라이언트는 직접 삽입되고 사용될 수 있습니다.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

원할 경우 형식화된 클라이언트에 대한 구성은 형식화된 클라이언트의 생성자보다는 `Startup.ConfigureServices`에 등록하는 동안 지정할 수 있습니다.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

형식화된 클라이언트 내에서 `HttpClient`을 완전히 캡슐화할 수 있습니다. 속성으로 공개하는 대신 공용 메서드를 제공하여 내부적으로 `HttpClient` 인스턴스를 호출할 수 있습니다.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

위의 코드에서 `HttpClient`은 개인 필드로 저장됩니다. 외부 호출을 하기 위한 모든 액세스는 `GetRepos` 메서드를 거칩니다.

### <a name="generated-clients"></a>생성된 클라이언트

`IHttpClientFactory`은 [Refit](https://github.com/paulcbetts/refit)과 같은 다른 타사 라이브러리와 함께 사용할 수 있습니다. Refit은 .NET에 대한 REST 라이브러리입니다. REST API를 라이브 인터페이스로 변환합니다. 인터페이스의 구현은 외부 HTTP를 호출하려면 `HttpClient`를 사용하여 `RestService`에 의해 동적으로 생성됩니다.

인터페이스와 회신은 외부 API와 해당 응답을 나타내기 위해 정의됩니다.

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

형식화된 클라이언트는 구현을 생성하기 위해 Refit를 사용하여 추가할 수 있습니다.

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

DI 및 Refit에서 제공한 구현을 통해 필요한 경우 정의된 인터페이스를 사용할 수 있습니다.

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

## <a name="outgoing-request-middleware"></a>나가는 요청 미들웨어

`HttpClient`에는 나가는 HTTP 요청을 위해 함께 연결될 수 있는 처리기 위임이라는 개념이 이미 있습니다. `IHttpClientFactory`은 각 명명된 클라이언트에 적용할 처리기를 쉽게 정의할 수 있습니다. 나가는 요청 미들웨어 파이프라인을 빌드하려면 여러 처리기의 연결 및 등록을 지원합니다. 이러한 처리기 각각은 나가는 요청 전후 작업을 수행할 수 있습니다. 이 패턴은 ASP.NET Core에서 인바운드 미들웨어 파이프라인과 비슷합니다. 패턴은 캐싱, 오류 처리, serialization 및 로깅을 포함하여 HTTP 요청을 둘러싼 교차 편집 문제를 관리할 메커니즘을 제공합니다.

처리기를 만들려면 <xref:System.Net.Http.DelegatingHandler>에서 파생되는 클래스를 정의합니다. 파이프라인의 다음 처리기로 요청을 전달하기 전에 코드를 실행하려면 `SendAsync` 메서드를 재정의합니다.

[!code-csharp[Main](http-requests/samples/2.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

위의 코드에서는 기본 처리기를 정의합니다. `X-API-KEY` 헤더가 요청에 포함되었는지 확인합니다. 헤더가 누락된 경우 HTTP 호출을 방지하고 적합한 응답을 반환할 수 있습니다.

등록 동안 하나 이상의 처리기가 `HttpClient`에 대한 구성에 추가될 수 있습니다. 이 작업은 <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>에 대한 확장 메서드를 통해 수행합니다.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet5)]

::: moniker range=">= aspnetcore-2.2"

위의 코드에서 `ValidateHeaderHandler`은 DI에 등록됩니다. `IHttpClientFactory`는 각 처리기에 대해 별도의 DI 범위를 만듭니다. 처리기는 모든 범위의 서비스에서 사용할 수 있습니다. 처리기가 삭제되면 처리기에서 사용하는 서비스가 삭제됩니다.

일단 등록되면 처리기에 대한 형식으로 전달하면서 <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*>을 호출할 수 있습니다.

::: moniker-end

::: moniker range="< aspnetcore-2.2"

위의 코드에서 `ValidateHeaderHandler`은 DI에 등록됩니다. 처리기는 범위가 지정되지 않은, 임시 서비스로 DI에 등록**되어야** 합니다. 처리기가 범위가 지정된 서비스로 등록되고 처리기에서 사용하는 서비스가 삭제 가능한 경우, 처리기가 범위를 벗어나기 전에 처리기의 서비스가 삭제될 수 있으며 그러면 처리기에서 오류가 발생합니다.

일단 등록되면 처리기 형식으로 전달하여 <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*>를 호출할 수 있습니다.

::: moniker-end

여러 처리기를 실행해야 하는 순서에 따라 등록할 수 있습니다. 각 처리기는 최종 `HttpClientHandler`가 요청을 실행할 때까지 다음 처리기를 래핑합니다.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

다음 방법 중 하나를 사용하여 메시지 처리기와 요청별 상태를 공유하세요.

* `HttpRequestMessage.Properties`를 사용하여 데이터를 처리기로 전달합니다.
* `IHttpContextAccessor`를 사용하여 현재 요청에 액세스합니다.
* 사용자 지정 `AsyncLocal` 스토리지 개체를 만들어 데이터를 전달합니다.

## <a name="use-polly-based-handlers"></a>Polly 기반 처리기 사용

`IHttpClientFactory`은 [Polly](https://github.com/App-vNext/Polly)라는 유명한 타사 라이브러리와 통합됩니다. Polly는 .NET에 대한 포괄적인 복원 력 및 일시적인 오류 처리 라이브러리입니다. Polly는 개발자가 다시 시도, 회로 차단기, 시간 제한, Bulkhead 격리 및 대체(Fallback) 같은 정책을 유연하고 스레드로부터 안전한 방식으로 표현하도록 허용합니다.

구성된 `HttpClient` 인스턴스를 통해 Polly 정책을 사용할 수 있도록 확장 메서드가 제공됩니다. Polly 확장은 [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet 패키지에서 사용할 수 있습니다. 이 패키지는 [Microsoft.AspNetCore.App 메타패키지](xref:fundamentals/metapackage-app)에 포함되어 있지 않습니다. 확장을 사용하려면 명시적 `<PackageReference />`가 프로젝트에 포함되어야 합니다.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/HttpClientFactorySample.csproj?highlight=10)]

이 패키지를 복원한 후 확장 메서드는 클라이언트에 Polly 기반 처리기를 추가하도록 지원할 수 있습니다.

### <a name="handle-transient-faults"></a>일시적인 오류 처리

가장 일반적인 오류는 외부 HTTP 호출이 일시적인 경우 발생합니다. 일시적인 오류를 처리하도록 정책을 정의할 수 있는 `AddTransientHttpErrorPolicy`이라고 하는 편리한 확장 메서드가 포함됩니다. 이 확장 메서드로 구성된 정책은 `HttpRequestException`, HTTP 5xx 응답 및 HTTP 408 응답을 처리합니다.

`AddTransientHttpErrorPolicy` 확장은 `Startup.ConfigureServices` 내에서 사용할 수 있습니다. 확장은 가능한 일시적 오류를 나타내는 오류를 처리하기 위해 구성된 `PolicyBuilder` 개체에 대한 액세스를 제공합니다.

[!code-csharp[Main](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet7)]

위의 코드에서 `WaitAndRetryAsync` 정책이 정의되어 있습니다. 실패한 요청은 최대 세 번까지 다시 시도되며 시도 간 600밀리초의 지연 간격을 둡니다.

### <a name="dynamically-select-policies"></a>동적으로 정책 선택

Polly 기반 처리기를 추가하는 데 사용될 수 있는 추가 확장 메서드가 존재합니다. 이러한 하나의 확장은 여러 오버로드가 있는 `AddPolicyHandler`입니다. 하나의 오버로드는 적용할 정책을 정의할 때 요청을 검사할 수 있습니다.

[!code-csharp[Main](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

위의 코드에서 나가는 요청이 GET인 경우 10초 시간 제한이 적용됩니다. 다른 HTTP 메서드의 경우 30초 시간 제한이 사용됩니다.

### <a name="add-multiple-polly-handlers"></a>여러 Polly 처리기 추가

향상된 기능을 제공하려면 Polly 정책을 중첩하는 것이 일반적입니다.

[!code-csharp[Main](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

앞의 예제에서 두 개의 처리기가 추가됩니다. 첫 번째 처리기는 재시도 정책을 추가하기 위해 `AddTransientHttpErrorPolicy` 확장을 사용합니다. 실패한 요청은 최대 세 번까지 다시 시도됩니다. `AddTransientHttpErrorPolicy`에 대한 두 번째 호출은 회로 차단기 정책을 추가합니다. 5번의 시도가 순차적으로 실패하는 경우 추가적인 외부 요청은 30초 동안 차단됩니다. 회로 차단기 정책은 상태 저장입니다. 이 클라이언트를 통한 모든 호출은 동일한 회로 상태를 공유합니다.

### <a name="add-policies-from-the-polly-registry"></a>Polly 레지스트리에서 정책 추가

정기적으로 사용되는 정책의 관리 방식은 정책을 한 번 정의하고 `PolicyRegistry`에 등록합니다. 정책을 사용하여 레지스트리에서 처리기를 추가할 수 있는 확장 메서드가 제공됩니다.

[!code-csharp[Main](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet10)]

위의 코드에서 두 정책은 `PolicyRegistry`가 `ServiceCollection`에 추가될 때 등록됩니다. 레지스트리에서 정책을 사용하기 위해 `AddPolicyHandlerFromRegistry` 메서드가 사용되어 적용할 정책의 이름을 전달합니다.

`IHttpClientFactory` 및 Polly 통합에 대한 추가 정보는 [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory)에서 확인할 수 있습니다.

## <a name="httpclient-and-lifetime-management"></a>HttpClient 및 수명 관리

`CreateClient`가 `IHttpClientFactory`에서 호출될 때마다 새 `HttpClient` 인스턴스가 반환됩니다. 명명된 클라이언트마다 <xref:System.Net.Http.HttpMessageHandler>가 있습니다. 팩터리는 `HttpMessageHandler` 인스턴스의 수명을 관리합니다.

`IHttpClientFactory`는 리소스 사용을 줄이기 위해 팩터리에서 만든 `HttpMessageHandler` 인스턴스를 풀링합니다. 새 `HttpClient` 인스턴스의 수명이 만료되지 않은 경우 해당 인스턴스를 만들 때 `HttpMessageHandler` 인스턴스를 풀에서 다시 사용할 수 있습니다.

일반적으로 각 처리기가 자체 기본 HTTP 연결을 관리하므로 처리기의 풀링이 적합합니다. 필요한 것보다 많은 처리기를 만들면 연결 지연이 발생할 수 있습니다. 또한 일부 처리기는 무한정으로 연결을 열어 놓아 처리기가 DNS 변경에 대응하는 것을 방지할 수 있습니다.

기본 처리기 수명은 2분입니다. 명명된 클라이언트별 기준으로 기본값을 재정의할 수 있습니다. 재정의하려면 클라이언트를 만들 때 반환되는 `IHttpClientBuilder`에서 <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*>을 호출합니다.

[!code-csharp[Main](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet11)]

클라이언트의 삭제는 필요하지 않습니다. 삭제는 나가는 요청을 취소하고 <xref:System.IDisposable.Dispose*>를 호출한 후에는 지정된 `HttpClient` 인스턴스가 사용될 수 없도록 보장합니다. `IHttpClientFactory`는 `HttpClient` 인스턴스에서 사용되는 리소스를 추적하고 삭제합니다. `HttpClient` 인스턴스는 일반적으로 삭제가 필요하지 않은 .NET 개체로 처리될 수 있습니다.

긴 기간 동안 단일 `HttpClient` 인스턴스를 활성 상태로 유지하는 것은 `IHttpClientFactory`의 시작 전에 사용되던 일반적인 패턴입니다. 이 패턴은 `IHttpClientFactory`로 마이그레이션한 후에는 필요하지 않습니다.

## <a name="logging"></a>로깅

`IHttpClientFactory`을 통해 만든 클라이언트는 모든 요청에 대한 로그 메시지를 기록합니다. 기본 로그 메시지를 보려면 로깅 구성에서 적절한 정보 수준을 사용하도록 설정합니다. 요청 헤더의 로깅 등의 추가 로깅은 추적 수준에서만 포함됩니다.

각 클라이언트에 사용되는 로그 범주는 클라이언트의 이름을 포함합니다. 예를 들어, *MyNamedClient*라는 클라이언트는 `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`의 범주를 사용하여 메시지를 기록합니다. *LogicalHandler*라는 접미사가 있는 메시지는 요청 처리기 파이프라인 외부에서 발생합니다. 요청 시 파이프라인의 다른 모든 처리기에서 이를 처리하기 전에 메시지가 기록됩니다. 응답 시 다른 모든 파이프라인 처리기가 응답을 받은 후에 메시지가 기록됩니다.

로깅은 요청 처리기 파이프라인 내부에서도 발생합니다. *MyNamedClient* 예제에서 해당 메시지는 로그 범주 `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`에 대해 기록됩니다. 요청의 경우 이는 요청이 네트워크에서 전송되기 직전 및 다른 모든 처리기가 실행된 후에 발생합니다. 응답 시 이 로깅은 처리기 파이프라인을 통해 응답이 다시 전달되기 전의 응답 상태를 포함합니다.

파이프라인 외부 및 내부에서 로깅을 사용하도록 설정하면 다른 파이프라인 처리기가 수행한 변경 내용을 검사할 수 있습니다. 예를 들어 여기에는 요청 헤더 또는 응답 상태 코드에 대한 변경이 포함될 수 있습니다.

로그 범주에 클라이언트의 이름을 포함하는 것은 필요한 경우 명명된 특정 클라이언트에 대한 로그 필터링을 수행할 수 있습니다.

## <a name="configure-the-httpmessagehandler"></a>HttpMessageHandler 구성

클라이언트에서 사용한 내부 `HttpMessageHandler`의 구성을 제어하는 것이 필요할 수 있습니다.

`IHttpClientBuilder`은 명명된 또는 형식화된 클라이언트를 추가할 때 반환됩니다. <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> 확장 메서드는 대리자를 정의하는 데 사용될 수 있습니다. 대리자는 해당 클라이언트가 사용한 기본 `HttpMessageHandler`을 만들고 구성하는 데 사용됩니다.

[!code-csharp[Main](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet12)]

## <a name="additional-resources"></a>추가 자료

* [HttpClientFactory를 사용하여 복원력 있는 HTTP 요청 구현](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [HttpClientFactory 및 Polly 정책을 통해 지수 백오프를 사용하여 HTTP 호출 다시 시도 구현](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [회로 차단기 패턴 구현](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)