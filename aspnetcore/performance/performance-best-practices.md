---
title: ASP.NET 핵심 성과 모범 사례
author: mjrousos
description: ASP.NET 핵심 앱의 성능을 높이고 일반적인 성능 문제를 방지하는 팁입니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 04/06/2020
no-loc:
- SignalR
uid: performance/performance-best-practices
ms.openlocfilehash: 068a35fbe410dad24030fe68c0dfd062b402212c
ms.sourcegitcommit: f0aeeab6ab6e09db713bb9b7862c45f4d447771b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80977186"
---
# <a name="aspnet-core-performance-best-practices"></a>ASP.NET 핵심 성과 모범 사례

[Mike Rousos](https://github.com/mjrousos) 작성

이 문서에서는 ASP.NET 핵심을 통해 성능 모범 사례에 대한 지침을 제공합니다.

## <a name="cache-aggressively"></a>공격적으로 캐시

캐싱은 이 문서의 여러 부분에서 설명합니다. 자세한 내용은 <xref:performance/caching/response>을 참조하세요.

## <a name="understand-hot-code-paths"></a>핫 코드 경로 이해

이 문서에서 *핫 코드 경로는* 자주 호출되는 코드 경로로 정의되며 실행 시간의 상당 부분을 발생합니다. 핫 코드 경로는 일반적으로 앱 확장 및 성능을 제한하며 이 문서의 여러 부분에서 설명합니다.

## <a name="avoid-blocking-calls"></a>통화 차단 방지

ASP.NET 핵심 앱은 많은 요청을 동시에 처리하도록 설계되어야 합니다. 비동기 API를 사용하면 작은 스레드 풀에서 호출 차단을 기다리지 않고 수천 개의 동시 요청을 처리할 수 있습니다. 스레드는 장기 실행 동기 작업을 완료하는 대신 다른 요청에서 작업할 수 있습니다.

ASP.NET Core 앱의 일반적인 성능 문제는 비동기일 수 있는 호출을 차단하는 것입니다. 많은 동기 차단 호출로 인해 [스레드 풀 이 끊기고](https://blogs.msdn.microsoft.com/vancem/2018/10/16/diagnosing-net-core-threadpool-starvation-with-perfview-why-my-service-is-not-saturating-all-cores-or-seems-to-stall/) 응답 시간이 저하됩니다.

**하지 마십시오**:

* [Task.Wait](/dotnet/api/system.threading.tasks.task.wait) 또는 [Task.Result를](/dotnet/api/system.threading.tasks.task-1.result)호출하여 비동기 실행을 차단합니다.
* 공통 코드 경로에서 잠금을 수집합니다. ASP.NET Core 앱은 코드를 병렬로 실행하도록 설계할 때 가장 성능이 뛰어납니다.
* [Task.Run을](/dotnet/api/system.threading.tasks.task.run) 호출하고 즉시 기다립니다. ASP.NET 코어는 이미 일반 스레드 풀 스레드에서 앱 코드를 실행하므로 Task.Run을 호출하면 불필요한 스레드 풀 일정만 발생합니다. 예약된 코드가 스레드를 차단하더라도 Task.Run은 이를 방지하지 못합니다.

**할**일 :

* [핫 코드 경로를](#understand-hot-code-paths) 비동기로 만듭니다.
* 비동기 API를 사용할 수 있는 경우 데이터 액세스, I/O 및 장기 실행 API를 비동기로 호출합니다. [Task.Run을](/dotnet/api/system.threading.tasks.task.run) 사용하여 동기화 API를 비동기로 만들지 **마십시오.**
* 컨트롤러/Razor 페이지 작업을 비동기로 만듭니다. 전체 호출 스택은 [비동기/await](/dotnet/csharp/programming-guide/concepts/async/) 패턴의 이점을 누리기 위해 비동기입니다.

[PerfView와](https://github.com/Microsoft/perfview)같은 프로파일러는 [스레드 풀에](/windows/desktop/procthread/thread-pools)자주 추가되는 스레드를 찾는 데 사용할 수 있습니다. 이 `Microsoft-Windows-DotNETRuntime/ThreadPoolWorkerThread/Start` 이벤트는 스레드 풀에 추가된 스레드를 나타냅니다. <!--  For more information, see [async guidance docs](TBD-Link_To_Davifowl_Doc)  -->

## <a name="minimize-large-object-allocations"></a>대규모 개체 할당 최소화

[.NET Core 가비지 수집기는](/dotnet/standard/garbage-collection/) ASP.NET Core 앱에서 자동으로 메모리 할당 및 릴리스를 관리합니다. 자동 가비지 수집은 일반적으로 개발자가 메모리가 해제되는 방법이나 시기에 대해 걱정할 필요가 없다는 것을 의미합니다. 그러나 참조되지 않은 개체를 정리하는 데는 CPU 시간이 걸리므로 개발자는 [핫 코드 경로에서](#understand-hot-code-paths)개체 할당을 최소화해야 합니다. 가비지 수집은 특히 큰 개체(> 85K 바이트)에서 비용이 많이 듭니다. 큰 개체는 [큰 개체 힙에](/dotnet/standard/garbage-collection/large-object-heap) 저장되며 정리하려면 전체(2세대) 가비지 수집이 필요합니다. 세대 0 및 1 세대 컬렉션과 달리 2세대 컬렉션에는 앱 실행을 일시적으로 중단해야 합니다. 큰 개체의 빈번한 할당 및 할당 해제는 일관되지 않은 성능을 유발할 수 있습니다.

권장 사항:

* 자주 사용되는 큰 개체를 캐싱하는 것이 **좋습니다.** 큰 개체를 캐싱하면 비용이 많이 드는 할당이 방지됩니다.
* [ArrayPool\<T>](/dotnet/api/system.buffers.arraypool-1) 사용하여 큰 배열을 저장하여 풀 버퍼를 **수행합니다.**
* [핫 코드 경로에](#understand-hot-code-paths)수명이 짧은 많은 큰 개체를 **할당하지 마십시오.**

앞의 메모리 문제는 [PerfView에서](https://github.com/Microsoft/perfview) 가비지 콜렉션(GC) 통계를 검토하고 검사하여 진단할 수 있습니다.

* 가비지 수집 일시 중지 시간입니다.
* 가비지 수집에 소요되는 프로세서 시간의 백분율입니다.
* 세대 0, 1 및 2인 가비지 콜렉션 수입니다.

자세한 내용은 [가비지 수집 및 성능을](/dotnet/standard/garbage-collection/performance)참조하십시오.

## <a name="optimize-data-access-and-io"></a>데이터 액세스 및 I/O 최적화

데이터 저장소 및 기타 원격 서비스와의 상호 작용은 ASP.NET Core 앱에서 가장 느린 부분인 경우가 많습니다. 데이터를 효율적으로 읽고 쓰는 것은 성능 향상을 위해 매우 중요합니다.

권장 사항:

* 모든 데이터 액세스 API를 비동기적으로 **호출합니다.**
* 필요한 것보다 많은 데이터를 **검색하지 마십시오.** 현재 HTTP 요청에 필요한 데이터만 반환하는 쿼리를 작성합니다.
* 약간 오래된 데이터가 허용되는 경우 데이터베이스 또는 원격 서비스에서 자주 액세스하는 데이터를 캐싱하는 **것이** 좋습니다. 시나리오에 따라 메모리 [캐시](xref:performance/caching/memory) 또는 [분산 캐시를](xref:performance/caching/distributed)사용합니다. 자세한 내용은 <xref:performance/caching/response>을 참조하세요.
* 네트워크 **왕복을 최소화하십시오.** 목표는 여러 호출이 아닌 단일 호출에서 필요한 데이터를 검색하는 것입니다.
* 읽기 전용 목적으로 데이터에 액세스할 때 엔터티 프레임워크 Core에서 [추적 하지 않는 쿼리를](/ef/core/querying/tracking#no-tracking-queries) 사용 **하십시오.** EF Core는 추적 이없음 쿼리의 결과를 보다 효율적으로 반환할 수 있습니다.
* 데이터베이스에서 필터링을 수행되도록 LINQ `.Select`쿼리(예: `.Sum` `.Where`또는 문)를 **필터링하고** 집계합니다.
* **Do** EF Core가 클라이언트의 일부 쿼리 연산자에서 해결되어 비효율적인 쿼리 실행으로 이어질 수 있습니다. 자세한 내용은 [클라이언트 평가 성능 문제를](/ef/core/querying/client-eval#client-evaluation-performance-issues)참조하십시오.
* "N + 1" SQL 쿼리를 실행될 수 있는 컬렉션에서 프로젝션 쿼리를 사용하지 **마십시오.** 자세한 내용은 [상관 하위 쿼리 최적화를](/ef/core/what-is-new/ef-core-2.1#optimization-of-correlated-subqueries)참조하십시오.

EF [고성능은](/ef/core/what-is-new/ef-core-2.0#explicitly-compiled-queries) 대규모 앱의 성능을 향상시킬 수 있는 접근 방식을 참조하십시오.

* [DbContext 풀링](/ef/core/what-is-new/ef-core-2.0#dbcontext-pooling)
* [명시적으로 컴파일된 쿼리](/ef/core/what-is-new/ef-core-2.0#explicitly-compiled-queries)

코드 베이스를 커밋하기 전에 이전 고성능 접근 방식의 영향을 측정하는 것이 좋습니다. 컴파일된 쿼리의 추가 복잡성으로 는 성능 향상을 정당화하지 못할 수 있습니다.

[응용 프로그램 인사이트](/azure/application-insights/app-insights-overview) 또는 프로파일링 도구를 사용하여 데이터에 액세스하는 데 소요된 시간을 검토하여 쿼리 문제를 검색할 수 있습니다. 또한 대부분의 데이터베이스는 자주 실행되는 쿼리와 관련된 통계를 사용할 수 있도록 합니다.

## <a name="pool-http-connections-with-httpclientfactory"></a>HTTP클라이언트팩토리를 통한 풀 HTTP 연결

[HttpClient는](/dotnet/api/system.net.http.httpclient) 인터페이스를 `IDisposable` 구현하지만 재사용을 위해 설계되었습니다. 닫힌 `HttpClient` 인스턴스는 짧은 시간 `TIME_WAIT` 동안 소켓을 상태로 둡니다. `HttpClient` 개체를 만들고 삭제하는 코드 경로가 자주 사용되는 경우 앱에서 사용 가능한 소켓을 소모할 수 있습니다. [HttpClientFactory는](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests) 이 문제에 대한 해결책으로 ASP.NET 코어 2.1에 도입되었습니다. HTTP 연결을 풀링하여 성능과 안정성을 최적화합니다.

권장 사항:

* 인스턴스를 직접 만들고 **삭제하지 마십시오.** `HttpClient`
* [HttpClientFactory를](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests) 사용하여 `HttpClient` 인스턴스를 **검색합니다.** 자세한 내용은 [HttpClientFactory 사용을 참조하여 복원력 있는 HTTP 요청을 구현합니다.](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)

## <a name="keep-common-code-paths-fast"></a>공통 코드 경로 의 빠른 유지

모든 코드가 빠르도록 할 수 있습니다. 자주 호출되는 코드 경로는 최적화하는 데 가장 중요합니다. 여기에는 다음이 포함됩니다.

* 앱의 요청 처리 파이프라인의 미들웨어 구성 요소, 특히 미들웨어는 파이프라인초기에 실행됩니다. 이러한 구성 요소는 성능에 큰 영향을 미칩니다.
* 모든 요청 또는 요청당 여러 번 실행되는 코드입니다. 예를 들어 사용자 지정 로깅, 권한 부여 처리기 또는 일시적인 서비스의 초기화를 예로 들 수 있습니다.

권장 사항:

* 장기 실행 작업과 사용자 지정 미들웨어 구성 요소를 사용하지 **마십시오.**
* [Visual Studio 진단 도구](/visualstudio/profiling/profiling-feature-tour) 또는 [PerfView와](https://github.com/Microsoft/perfview)같은 성능 프로파일링 도구를 **사용하여** 핫 코드 [경로를 식별합니다.](#understand-hot-code-paths)

## <a name="complete-long-running-tasks-outside-of-http-requests"></a>HTTP 요청 외부에서 장기 실행 작업 완료

ASP.NET Core 앱에 대한 대부분의 요청은 컨트롤러 또는 페이지 모델에서 필요한 서비스를 호출하고 HTTP 응답을 반환하여 처리할 수 있습니다. 장기 실행 작업이 포함된 일부 요청의 경우 전체 요청 응답 프로세스를 비동기로 만드는 것이 좋습니다.

권장 사항:

* 장기 실행 작업이 일반 HTTP 요청 처리의 일부로 완료될 때까지 **기다리지 마십시오.**
* [Azure Function을](/azure/azure-functions/)사용하여 [백그라운드 서비스](xref:fundamentals/host/hosted-services) 또는 프로세스 에서 벗어난 장기 실행 요청을 처리하는 **것이** 좋습니다. 프로세스 외 작업을 완료하는 것은 CPU 집약적인 작업에 특히 유용합니다.
* 과 같은 [SignalR](xref:signalr/introduction)실시간 통신 옵션을 **사용하여** 클라이언트와 비동기적으로 통신합니다.

## <a name="minify-client-assets"></a>클라이언트 자산 최소화

복잡한 프런트 엔드가 있는 ASP.NET 핵심 앱은 많은 자바스크립트, CSS 또는 이미지 파일을 자주 제공합니다. 초기 로드 요청의 성능을 다음과 같은 경우 개선할 수 있습니다.

* 여러 파일을 하나로 결합하는 번들링입니다.
* 공백 및 주석을 제거하여 파일 크기를 줄이는 Minifying.

권장 사항:

* 클라이언트 자산을 번들링하고 축소하기 위해 ASP.NET Core의 [기본 제공 지원을](xref:client-side/bundling-and-minification) **사용하십시오.**
* 복잡한 클라이언트 자산 관리를 위해 [Webpack과](https://webpack.js.org/)같은 다른 타사 도구를 **고려하십시오.**

## <a name="compress-responses"></a>응답 압축

 응답 크기를 줄이면 일반적으로 앱의 응답성이 크게 증가합니다. 페이로드 크기를 줄이는 한 가지 방법은 앱의 응답을 압축하는 것입니다. 자세한 내용은 [응답 압축](xref:performance/response-compression)을 참조하십시오.

## <a name="use-the-latest-aspnet-core-release"></a>최신 ASP.NET 코어 릴리스 사용

ASP.NET 코어의 각 릴리스에는 성능 향상이 포함됩니다. .NET 코어 및 ASP.NET 코어의 최적화는 최신 버전이 일반적으로 이전 버전보다 우수함을 의미합니다. 예를 들어 .NET Core 2.1은 컴파일된 정규식에 대한 지원을 추가하고 [Span\<T>. ](https://msdn.microsoft.com/magazine/mt814808.aspx) ASP.NET 코어 2.2 HTTP/2에 대한 지원이 추가되었습니다. [ASP.NET 코어 3.0은](xref:aspnetcore-3.0) 메모리 사용량을 줄이고 처리량을 개선하는 많은 개선 사항이 추가되었습니다. 성능이 우선 순위인 경우 현재 버전의 ASP.NET 코어로 업그레이드하는 것이 좋습니다.

## <a name="minimize-exceptions"></a>예외 최소화

예외는 드물어야 합니다. 예외를 throw하고 catch하는 것은 다른 코드 흐름 패턴에 비해 느립니다. 따라서 정상적인 프로그램 흐름을 제어하는 데 예외를 사용해서는 안 됩니다.

권장 사항:

* 특히 [핫 코드 경로에서](#understand-hot-code-paths)일반 프로그램 흐름의 수단으로 throw 또는 catch 예외를 사용하지 **마십시오.**
* 예외를 일으킬 수 있는 조건을 감지하고 처리하는 논리를 앱에 **포함시겠습니까?**
* 비정상적이거나 예기치 않은 조건에 대해 예외를 throw하거나 **catch합니다.**

응용 프로그램 인사이트와 같은 앱 진단 도구는 성능에 영향을 줄 수 있는 앱의 일반적인 예외를 식별하는 데 도움이 될 수 있습니다.

## <a name="performance-and-reliability"></a>성능 및 신뢰성

다음 섹션에서는 성능 팁과 알려진 안정성 문제 및 솔루션을 제공합니다.

## <a name="avoid-synchronous-read-or-write-on-httprequesthttpresponse-body"></a>HttpRequest/HttpResponse 본문에서 동기 읽기 또는 쓰기 방지

ASP.NET 코어의 모든 I/O는 비동기입니다. 서버는 동기 `Stream` 및 비동기 오버로드가 모두 있는 인터페이스를 구현합니다. 비동기 스레드 풀 스레드를 차단 하지 않도록 하는 것이 좋습니다. 스레드를 차단하면 스레드 풀이 부족할 수 있습니다.

**이렇게 하지 마십시오.** 다음 예제에서는 <xref:System.IO.StreamReader.ReadToEnd*>을 사용합니다. 결과를 기다리도록 현재 스레드를 차단합니다. 이것은 [비동기를 통해 동기화의](https://github.com/davidfowl/AspNetCoreDiagnosticScenarios/blob/master/AsyncGuidance.md#warning-sync-over-async
)예입니다.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/MyFirstController.cs?name=snippet1)]

앞의 코드에서는 `Get` 전체 HTTP 요청 본문을 메모리로 동기적으로 읽습니다. 클라이언트가 느리게 업로드하는 경우 앱이 비동기를 통해 동기화를 수행하고 있습니다. Kestrel이 동기 읽기를 지원하지 **않기** 때문에 앱이 비동기를 통해 동기화됩니다.

**이 작업을 수행합니다.** 다음 예제에서는 <xref:System.IO.StreamReader.ReadToEndAsync*> 읽는 동안 스레드를 사용하고 차단하지 않습니다.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/MyFirstController.cs?name=snippet2)]

앞의 코드는 전체 HTTP 요청 본문을 비동기적으로 메모리로 읽습니다.

> [!WARNING]
> 요청이 큰 경우 전체 HTTP 요청 본문을 메모리로 읽으면 메모리 부족(OOM) 상태가 발생할 수 있습니다. OOM은 서비스 거부를 초래할 수 있습니다.  자세한 내용은 이 문서의 [큰 요청 본문 또는 응답 본문메모리를 읽지 마십시오.](#arlb)

**이 작업을 수행합니다.** 다음 예제는 버퍼링되지 않은 요청 본문을 사용하여 완전히 비동기입니다.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/MyFirstController.cs?name=snippet3)]

앞의 코드는 요청 본문을 C# 개체로 비동기적으로 해제합니다.

## <a name="prefer-readformasync-over-requestform"></a>요청보다 ReadFormAsync 선호합니다.양식

`HttpContext.Request.ReadFormAsync` 대신 `HttpContext.Request.Form`를 사용합니다.
`HttpContext.Request.Form`다음 조건에서만 안전하게 읽을 수 있습니다.

* 양식을 호출하여 `ReadFormAsync`읽었습니다.
* 캐시된 양식 값을 사용하여 읽습니다.`HttpContext.Request.Form`

**이렇게 하지 마십시오.** 다음 예제에서는 `HttpContext.Request.Form`을 사용합니다.  `HttpContext.Request.Form`[비동기를 통해 동기화를](https://github.com/davidfowl/AspNetCoreDiagnosticScenarios/blob/master/AsyncGuidance.md#warning-sync-over-async
) 사용 하 고 스레드 풀 기아로 이어질 수 있습니다.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/MySecondController.cs?name=snippet1)]

**이 작업을 수행합니다.** 다음 예제에서는 `HttpContext.Request.ReadFormAsync` 양식 본문을 비동기적으로 읽는 데 사용합니다.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/MySecondController.cs?name=snippet2)]

<a name="arlb"></a>

## <a name="avoid-reading-large-request-bodies-or-response-bodies-into-memory"></a>큰 요청 본문 또는 응답 본체를 메모리로 읽지 마십시오.

.NET에서 85KB보다 큰 모든 개체 할당은 큰 개체[힙(LOH)으로](https://blogs.msdn.microsoft.com/maoni/2006/04/19/large-object-heap/)끝납니다. 큰 개체는 두 가지 방법으로 비용이 많이 듭니다.

* 새로 할당된 큰 개체에 대한 메모리를 지워야 하기 때문에 할당 비용이 높습니다. CLR은 새로 할당된 모든 개체에 대한 메모리가 지워지도록 보장합니다.
* LOH는 힙의 나머지 부분과 함께 수집됩니다. LOH에는 전체 [가비지 수집](/dotnet/standard/garbage-collection/fundamentals) 또는 [Gen2 수집이](/dotnet/standard/garbage-collection/fundamentals#generations)필요합니다.

이 [블로그 게시물에서는](https://adamsitnik.com/Array-Pool/#the-problem) 문제를 간결하게 설명합니다.

> 큰 개체가 할당되면 Gen 2 개체로 표시됩니다. 작은 개체에 관해서는 Gen 0이 아닙니다. 그 결과 LOH에서 메모리가 부족하면 GC가 LOH뿐만 아니라 전체 관리 힙을 정리합니다. 따라서 LOH를 포함하여 0세대, 1세대 및 2세대를 정리합니다. 이를 전체 가비지 수집이라고 하며 가장 시간이 많이 소요되는 가비지 수집입니다. 많은 응용 프로그램의 경우 허용될 수 있습니다. 그러나 평균 웹 요청을 처리하기 위해 큰 메모리 버퍼가 거의 필요한 고성능 웹 서버의 경우는 그렇지 않습니다 (소켓에서 읽기, 압축 해제, JSON & 더).

큰 요청 또는 응답 본문을 단일 `byte[]` `string`또는 다음 으로 순진하게 저장합니다.

* LOH의 공간이 빠르게 부족할 수 있습니다.
* 전체 PC가 실행중이기 때문에 앱의 성능 문제가 발생할 수 있습니다.

## <a name="working-with-a-synchronous-data-processing-api"></a>동기 데이터 처리 API 작업

동기 읽기 및 쓰기만 지원하는 serializer/de-serializer를 사용하는 경우(예: [JSON.NET):](https://www.newtonsoft.com/json/help/html/Introduction.htm)

* 직렬화/디 serializer에 전달하기 전에 데이터를 비동기적으로 메모리에 버퍼링합니다.

> [!WARNING]
> 요청이 큰 경우 메모리 부족(OOM) 상태가 될 수 있습니다. OOM은 서비스 거부를 초래할 수 있습니다.  자세한 내용은 이 문서의 [큰 요청 본문 또는 응답 본문메모리를 읽지 마십시오.](#arlb)

ASP.NET 코어 3.0은 JSON 직렬화에 기본적으로 사용합니다. <xref:System.Text.Json> <xref:System.Text.Json>:

* JSON을 비동기적으로 읽고 씁니다.
* UTF-8 텍스트에 최적화되어 있습니다.
* 일반적으로 `Newtonsoft.Json`보다 성능이 높습니다.

## <a name="do-not-store-ihttpcontextaccessorhttpcontext-in-a-field"></a>필드에 IHttpContextAccessor.HttpContext를 저장하지 마십시오.

[IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext) 요청 `HttpContext` 스레드에서 액세스할 때 활성 요청의 반환 합니다. 필드 `IHttpContextAccessor.HttpContext` 나 변수에 저장 **해서는** 안 됩니다.

**이렇게 하지 마십시오.** 다음 `HttpContext` 예제에서는 필드를 저장 한 다음 나중에 사용 하려고 합니다.

[!code-csharp[](performance-best-practices/samples/3.0/MyType.cs?name=snippet1)]

앞의 코드는 생성자에서 null `HttpContext` 또는 잘못된 것을 자주 캡처합니다.

**이 작업을 수행합니다.** 다음 예제는 다음과 같은 것입니다.

* 필드에 <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> 저장합니다.
* 올바른 `HttpContext` 시간에 필드를 사용하고 을 `null`확인합니다.

[!code-csharp[](performance-best-practices/samples/3.0/MyType.cs?name=snippet2)]

## <a name="do-not-access-httpcontext-from-multiple-threads"></a>여러 스레드에서 HttpContext에 액세스하지 않음

`HttpContext`스레드가 안전하지 *않습니다.* 여러 `HttpContext` 스레드에서 병렬로 액세스하면 중단, 충돌 및 데이터 손상과 같은 정의되지 않은 동작이 발생할 수 있습니다.

**이렇게 하지 마십시오.** 다음 예제에서는 세 개의 병렬 요청을 만들고 나가는 HTTP 요청 전후에 들어오는 요청 경로를 기록합니다. 요청 경로는 여러 스레드에서 액세스할 수 있습니다.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/AsyncFirstController.cs?name=snippet1&highlight=25,28)]

**이 작업을 수행합니다.** 다음 예제에서는 세 개의 병렬 요청을 하기 전에 들어오는 요청의 모든 데이터를 복사합니다.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/AsyncFirstController.cs?name=snippet2&highlight=6,8,22,28)]

## <a name="do-not-use-the-httpcontext-after-the-request-is-complete"></a>요청이 완료된 후 HttpContext를 사용하지 마십시오.

`HttpContext`ASP.NET 코어 파이프라인에 활성 HTTP 요청이 있는 경우에만 유효합니다. 전체 ASP.NET Core 파이프라인은 모든 요청을 실행하는 대리자의 비동기 체인입니다. 이 `Task` 체인에서 반환된 반환이 `HttpContext` 완료되면 재활용됩니다.

**이렇게 하지 마십시오.** 다음 예제에서는 `async void` 첫 번째 `await` 요청에 도달하면 HTTP 요청을 완료할 수 있는 예제를 사용합니다.

* 이는 **항상** ASP.NET 핵심 애플 리케이션에서 나쁜 관행이다.
* HTTP 요청이 `HttpResponse` 완료된 후 액세스합니다.
* 프로세스를 충돌합니다.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/AsyncBadVoidController.cs?name=snippet1)]

**이 작업을 수행합니다.** 다음 예제에서는 `Task` a를 프레임워크에 반환하므로 작업이 완료될 때까지 HTTP 요청이 완료되지 않습니다.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/AsyncSecondController.cs?name=snippet1)]

## <a name="do-not-capture-the-httpcontext-in-background-threads"></a>백그라운드 스레드에서 HttpContext를 캡처하지 마십시오.

**이렇게 하지 마십시오.** 다음 예제에서는 클로저가 속성에서 를 캡처하고 있는 `HttpContext` 것을 `Controller` 보여 주습니다. 작업 항목이 다음을 수행할 수 있기 때문에 이는 잘못된 방법입니다.

* 요청 범위 외부에서 실행합니다.
* 잘못된 `HttpContext`내용을 읽으려고 합니다.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetFirstController.cs?name=snippet1)]

**이 작업을 수행합니다.** 다음 예제는 다음과 같은 것입니다.

* 요청 하는 동안 백그라운드 작업에 필요한 데이터를 복사 합니다.
* 컨트롤러에서 아무 것도 참조하지 않습니다.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetFirstController.cs?name=snippet2)]

백그라운드 작업은 호스팅된 서비스로 구현되어야 합니다. 자세한 내용은 [호스티드 서비스를 사용하는 백그라운드 작업](xref:fundamentals/host/hosted-services)을 참조하세요.

## <a name="do-not-capture-services-injected-into-the-controllers-on-background-threads"></a>백그라운드 스레드에서 컨트롤러에 주입된 서비스를 캡처하지 마십시오.

**이렇게 하지 마십시오.** 다음 예제에서는 클로저가 작업 `DbContext` 매개 `Controller` 변수에서 캡처하는 것을 보여 주습니다. 이것은 나쁜 관행입니다.  작업 항목은 요청 범위 외부에서 실행될 수 있습니다. 을 `ContosoDbContext` 사용하여 요청에 범위가 조정되어 `ObjectDisposedException`있습니다.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetSecondController.cs?name=snippet1)]

**이 작업을 수행합니다.** 다음 예제는 다음과 같은 것입니다.

* <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory> 백그라운드 작업 항목에 범위를 만들기 위해 을 삽입합니다. `IServiceScopeFactory`싱글톤입니다.
* 백그라운드 스레드에서 새 종속성 주입 범위를 만듭니다.
* 컨트롤러에서 아무 것도 참조하지 않습니다.
* 들어오는 요청에서 `ContosoDbContext` 캡처하지 않습니다.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetSecondController.cs?name=snippet2)]

다음 강조 표시된 코드는 다음과 같은 것입니다.

* 백그라운드 작업의 수명에 대한 범위를 만들고 서비스에서 서비스를 해결합니다.
* 올바른 `ContosoDbContext` 범위에서 사용합니다.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetSecondController.cs?name=snippet2&highlight=9-16)]

## <a name="do-not-modify-the-status-code-or-headers-after-the-response-body-has-started"></a>응답 본문이 시작된 후 상태 코드 또는 헤더를 수정하지 마십시오.

ASP.NET 코어는 HTTP 응답 본문을 버퍼링하지 않습니다. 응답을 처음 작성할 때:

* 헤더는 본체의 해당 청크와 함께 클라이언트로 전송됩니다.
* 더 이상 응답 헤더를 변경할 수 없습니다.

**이렇게 하지 마십시오.** 다음 코드는 응답이 이미 시작된 후 응답 헤더를 추가하려고 시도합니다.

[!code-csharp[](performance-best-practices/samples/3.0/Startup22.cs?name=snippet1)]

앞의 코드에서는 `context.Response.Headers["test"] = "test value";` 응답에 기록된 `next()` 경우 예외를 throw합니다.

**이 작업을 수행합니다.** 다음 예제는 헤더를 수정하기 전에 HTTP 응답이 시작되었는지 확인합니다.

[!code-csharp[](performance-best-practices/samples/3.0/Startup22.cs?name=snippet2)]

**이 작업을 수행합니다.** 다음 예제에서는 `HttpResponse.OnStarting` 응답 헤더가 클라이언트로 플러시되기 전에 헤더를 설정하는 데 사용됩니다.

응답이 시작되지 않은지 확인하면 응답 헤더가 작성되기 직전에 호출되는 콜백을 등록할 수 있습니다. 응답이 시작되지 않은지 확인합니다.

* 시간에 맞춰 헤더를 더하거나 재정의할 수 있는 기능을 제공합니다.
* 파이프라인의 다음 미들웨어에 대한 지식이 필요하지 않습니다.

[!code-csharp[](performance-best-practices/samples/3.0/Startup22.cs?name=snippet3)]

## <a name="do-not-call-next-if-you-have-already-started-writing-to-the-response-body"></a>응답 본문에 쓰기를 이미 시작한 경우 next() 호출하지 마십시오.

구성 요소는 응답을 처리하고 조작할 수 있는 경우에만 호출될 것으로 예상합니다.
