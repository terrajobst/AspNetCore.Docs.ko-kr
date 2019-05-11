---
title: ASP.NET Core 성능 모범 사례
author: mjrousos
description: 일반적인 성능 문제 방지 및 ASP.NET Core 앱에서 성능 향상에 대 한 팁입니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 05/10/2019
uid: performance/performance-best-practices
ms.openlocfilehash: 7651dff18f98c60057660c8946c3daa66d272f6a
ms.sourcegitcommit: ffe3ed7921ec6c7c70abaac1d10703ec9a43374c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/11/2019
ms.locfileid: "65536078"
---
# <a name="aspnet-core-performance-best-practices"></a>ASP.NET Core 성능 모범 사례

[Mike Rousos](https://github.com/mjrousos)

이 항목에서는 지침 성능에 대 한 ASP.NET Core를 사용 하 여 모범 사례.

<a name="hot"></a>

이 문서에는 *핫 코드 경로* 자주 호출 되는 및 실행 시간이 많이 발생 하는 코드 경로로 정의 됩니다. 핫 코드 경로 일반적으로 앱 확장 및 성능 제한 합니다.

## <a name="cache-aggressively"></a>적극적으로 캐시

캐시는이 문서의 여러 부분에서 설명 합니다. 자세한 내용은 <xref:performance/caching/response>을 참조하세요.

## <a name="avoid-blocking-calls"></a>호출 차단 방지

ASP.NET Core 앱을 동시에 많은 요청을 처리 하도록 설계 되어야 합니다. 비동기 Api 기다리지 호출을 차단 하 여 수천 개의 동시 요청을 처리 하는 스레드 풀을 수 있습니다. 장기 실행 동기 작업 완료를 기다리는 대신 스레드는 다른 요청에서 작업할 수 있습니다.

ASP.NET Core 앱의 일반적인 성능 문제는 비동기 될 수 있는 호출을 차단 합니다. 많은 동기식 차단 호출을 발생 시킬 [스레드 풀이 고갈](https://blogs.msdn.microsoft.com/vancem/2018/10/16/diagnosing-net-core-threadpool-starvation-with-perfview-why-my-service-is-not-saturating-all-cores-or-seems-to-stall/) 및 응답 시간 저하 됩니다.

**그렇지 않은**:

* 호출 하 여 비동기 실행을 차단 [Task.Wait](/dotnet/api/system.threading.tasks.task.wait) 하거나 [, task.result는](/dotnet/api/system.threading.tasks.task-1.result)합니다.
* 일반적인 코드 경로에서 잠금을 획득 합니다. ASP.NET Core 앱은 대부분의 고성능 병렬에서 코드를 실행 하도록 설계 하는 경우입니다.

**Do**:

* 확인 [핫 코드 경로](#hot) 비동기입니다.
* 데이터 액세스 및 장기 실행 작업 Api 비동기적으로 호출 합니다.
* 비동기화 컨트롤러/Razor 페이지 작업 합니다. 전체 호출 스택은의 이점을 얻기 위해서는 비동기 [async/await](/dotnet/csharp/programming-guide/concepts/async/) 패턴입니다.

프로파일러는와 같은 [PerfView](https://github.com/Microsoft/perfview)를 자주 추가 된 스레드를 찾는 데 사용할 수는 [스레드 풀](/windows/desktop/procthread/thread-pools)합니다. `Microsoft-Windows-DotNETRuntime/ThreadPoolWorkerThread/Start` 이벤트 스레드 풀에 추가 하는 스레드를 나타냅니다. <!--  For more information, see [async guidance docs](TBD-Link_To_Davifowl_Doc  -->

## <a name="minimize-large-object-allocations"></a>큰 개체 할당을 최소화 합니다.

<!-- TODO review Bill - replaced original .NET language below with .NET Core since this targets .NET Core -->
합니다 [.NET Core 가비지 수집기](/dotnet/standard/garbage-collection/) ASP.NET Core 앱에서 자동으로 메모리의 할당 및 해제를 관리 합니다. 자동 가비지 수집은 일반적으로 개발자가 메모리 해제 될 때나 하는 방법에 대해 걱정할 필요 하지 않다는 의미 합니다. 그러나 참조 되지 않은 개체를 정리 하는 CPU 시간, 개발자의 할당 개체를 최소화 해야 하므로 [핫 코드 경로](#hot)합니다. 가비지 수집은 특히 많이 사용 하므로 큰 개체 (> 85 K 바이트)입니다. 큰 개체에 저장 된 합니다 [대형 개체 힙](/dotnet/standard/garbage-collection/large-object-heap) (2 세대) 전체 필요 하 고 정리 하기 위해 가비지 수집 합니다. 0 세대 및 1 세대와 달리 2 세대 수집이 임시 앱 실행이 일시 중단을 필요합니다. 자주 할당 및 큰 개체의 할당 취소는 일관 되지 않은 성능을 발생할 수 있습니다.

권장 사항:

* **수행할** 자주 사용 되는 큰 개체를 캐시 하는 것이 좋습니다. 큰 개체 캐싱 방지 비용이 많이 드는 할당 합니다.
* **수행할** 를 사용 하 여 버퍼 풀을 [ `ArrayPool<T>` ](/dotnet/api/system.buffers.arraypool-1) 대형 배열을 저장 합니다.
* **하지** 다 수의 수명이 짧은 큰 개체에 할당할 [핫 코드 경로](#hot)합니다.

가비지 수집 (GC) 통계를 검토 하 여 앞의 예: 메모리 문제를 진단할 수 있습니다 [PerfView](https://github.com/Microsoft/perfview) 를 검사 합니다.

* 가비지 컬렉션 일시 중지 시간입니다.
* 프로세서 시간 비율 가비지 수집에 소요 됩니다.
* 가비지 수집 횟수는 0, 1 및 2 세대입니다.

자세한 내용은 [가비지 컬렉션 및 성능](/dotnet/standard/garbage-collection/performance)합니다.

## <a name="optimize-data-access"></a>데이터 액세스 최적화

데이터 저장소 및 기타 원격 서비스와의 상호 작용 ASP.NET Core 앱의 가장 느린 부분이 많습니다. 읽기 및 쓰기 데이터를 효율적으로 성능 향상을 위해 중요 한 경우

권장 사항:

* **수행할** 비동기적으로 모든 데이터 액세스 Api를 호출 합니다.
* **그렇지 않은** 필요한 것 보다 더 많은 데이터를 검색 합니다. 현재 HTTP 요청에 필요한 데이터만 반환 하는 쿼리를 작성 합니다.
* **수행할** 자주 캐싱 약간 오래 된 데이터 허용 되는 경우 데이터베이스 또는 원격 서비스에서 검색 된 데이터에 액세스 하는 것이 좋습니다. 시나리오에 따라 사용 된 [MemoryCache](xref:performance/caching/memory) 또는 [DistributedCache](xref:performance/caching/distributed)합니다. 자세한 내용은 <xref:performance/caching/response>을 참조하세요.
* **수행할** 최소화 네트워크 왕복이 합니다. 목표 여러 번 호출 하지 않고 한 번의 호출에 필요한 데이터를 검색 하는 것입니다.
* **수행할** 사용 하 여 [비 추적 쿼리](/ef/core/querying/tracking#no-tracking-queries) Entity Framework Core에 대 한 읽기 전용 데이터에 액세스 하는 경우에 합니다. EF Core는 보다 효율적으로 비 추적 쿼리 결과 반환할 수 있습니다.
* **수행할** 필터 및 집계 LINQ 쿼리 (사용 하 여 `.Where`를 `.Select`, 또는 `.Sum` 문과) 데이터베이스는 필터링이 수행 되도록 합니다.
* **수행할** EF Core 비효율적인 쿼리 실행을 야기할 수 있는 클라이언트에 대 한 일부 쿼리 연산자를 확인 하는 것이 좋습니다. 자세한 내용은 [클라이언트 평가 성능 문제](/ef/core/querying/client-eval#client-evaluation-performance-issues)합니다.
* **그렇지 않은** 프로젝션 쿼리를 사용 하 여 "N + 1" 실행 될 수 있는 컬렉션에 대해 SQL 쿼리. 자세한 내용은 [상관된 하위 쿼리 최적화](/ef/core/what-is-new/ef-core-2.1#optimization-of-correlated-subqueries)합니다.

참조 [EF 고성능](/ef/core/what-is-new/ef-core-2.0#explicitly-compiled-queries) 확장성이 뛰어난 앱의 성능을 향상 시킬 수 있는 방법에 대해:

* [DbContext 풀링](/ef/core/what-is-new/ef-core-2.0#dbcontext-pooling)
* [명시적으로 컴파일된 쿼리](/ef/core/what-is-new/ef-core-2.0#explicitly-compiled-queries)

앞의 코드 베이스를 커밋하기 전에 성능 우선 방법의 영향을 측정 하는 것이 좋습니다. 컴파일된 쿼리 복잡성 하지 성능 향상을 정당화할 수 있습니다.

쿼리 시간을 검토 하 여 문제를 검색할 수 있습니다 사용 하 여 데이터를 액세스 하는 데 걸린 [Application Insights](/azure/application-insights/app-insights-overview) 또는 프로 파일링 도구입니다. 대부분의 데이터베이스도 사용 가능 통계 자주 실행된 된 쿼리에 대 한 합니다.

## <a name="pool-http-connections-with-httpclientfactory"></a>HttpClientFactory 사용 하 여 HTTP 풀 연결

하지만 [HttpClient](/dotnet/api/system.net.http.httpclient) 구현 된 `IDisposable` 인터페이스를 다시 사용할 수 있도록 설계 되었습니다. 닫힌 `HttpClient` 인스턴스 소켓에서 열어 둔 상태로 `TIME_WAIT` 짧은 기간에 대 한 상태입니다. 코드 경로 만들고 삭제 하는 경우 `HttpClient` 개체 자주 사용 하는, 앱에 사용 가능한 소켓 소진 할 수 있습니다. [HttpClientFactory](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests) ASP.NET Core 2.1에서이 문제에 대 한 솔루션으로 도입 되었습니다. 성능 및 안정성을 최적화 하기 위해 풀링 HTTP 연결을 처리 합니다.

권장 사항:

* **하지** 만들고 삭제 `HttpClient` 인스턴스를 직접.
* **수행할** 사용 하 여 [HttpClientFactory](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests) 검색할 `HttpClient` 인스턴스. 자세한 내용은 [사용 하 여 복원 력 있는 HTTP 요청을 구현 하는 HttpClientFactory](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)합니다.

## <a name="keep-common-code-paths-fast"></a>빠른 일반적인 코드 경로 유지 합니다.

프로그램 코드를 빨리, 자주 호출 코드 경로 모두 최적화에 가장 중요 합니다.

* 앱의 요청 처리 파이프라인에 미들웨어 구성 요소, 특히 미들웨어 파이프라인 초기에 실행 합니다. 이러한 구성 요소 성능에 큰 영향을 미칠 있습니다.
* 모든 요청에 대해 또는 여러 번 요청에 따라 실행 되는 코드입니다. 예를 들어, 사용자 지정 로깅, 권한 부여 처리기 또는 일시적인 서비스를 초기화 합니다.

권장 사항:

* **그렇지 않은** 장기 실행 작업을 사용 하 여 사용자 지정 미들웨어 구성 요소를 사용 합니다.
* **수행할** 성능 같은 프로 파일링 도구를 사용 하 여 [Visual Studio Diagnostic Tools](/visualstudio/profiling/profiling-feature-tour) 또는 [PerfView](https://github.com/Microsoft/perfview))를 식별 하려면 [핫 코드 경로](#hot)합니다.

## <a name="complete-long-running-tasks-outside-of-http-requests"></a>장기 실행 작업 외부 HTTP 요청을 완료 합니다.

컨트롤러나 페이지 모델이 필요한 서비스를 호출 하 고 HTTP 응답을 반환 하 여 ASP.NET Core 앱에 대 한 대부분의 요청을 처리할 수 있습니다. 장기 실행 작업을 포함 하는 일부 요청에 대 한 전체 요청-응답 프로세스를 비동기화 하는 것이 좋습니다.

권장 사항:

* **그렇지 않은** 장기 실행 작업이 일반 HTTP 요청 처리의 일부로 완료 될 때까지 기다립니다.
* **수행** 사용 하 여 장기 실행 요청을 처리 하는 것이 좋습니다 [백그라운드 서비스로](xref:fundamentals/host/hosted-services) 안팎으로 프로세스를 [Azure Function](/azure/azure-functions/)합니다. 작업-out-of-process 완료 CPU 집약적인 작업에 특히 유용 합니다.
* **수행할** 같은 실시간 통신 옵션을 사용 하 여 [SignalR](xref:signalr/introduction), 클라이언트를 비동기적으로 통신할 수 있습니다.

## <a name="minify-client-assets"></a>클라이언트 자산 축소

복잡 한 프런트 엔드를 사용 하 여 ASP.NET Core 앱 많은 JavaScript, CSS 또는 이미지 파일에 자주 사용 됩니다. 초기 로드 요청의 성능은 하 여 향상 시킬 수 있습니다.

* 하나로 여러 파일을 결합 묶음입니다.
* 공백 및 주석을 제거 하 여 파일의 크기를 줄입니다 축소,입니다.

권장 사항:

* **수행할** ASP.NET Core를 사용 하 여 [기본 제공 지원](xref:client-side/bundling-and-minification) 클라이언트 자산 묶음 및 축소에 대 한 합니다.
* **수행할** 다른 타사 도구는 같은 것이 좋습니다 [Webpack](https://webpack.js.org/), 복잡 한 클라이언트 자산 관리에 대 한 합니다.

## <a name="compress-responses"></a>응답 압축

 일반적으로 응답의 크기를 줄이면 앱의 응답성 데이터는 종종 크게 증가 합니다. 페이로드 크기를 줄이는 한 가지 방법은 응용 프로그램의 응답을 압축 하는 경우 자세한 내용은 [응답 압축](xref:performance/response-compression)합니다.

## <a name="use-the-latest-aspnet-core-release"></a>최신 ASP.NET Core 릴리스를 사용 합니다.

ASP.NET Core의 새 릴리스가 나올 때마다 성능 향상을 포함합니다. .NET Core 및 ASP.NET Core에서 최적화 최신 버전이 이전 버전 보다 성능이 더 일반적으로 우수한 것을 의미 합니다. 예를 들어,.NET Core 2.1 지원이 추가 되었습니다 컴파일된 정규식을에서 기쁨 [ `Span<T>` ](https://msdn.microsoft.com/magazine/mt814808.aspx)합니다. HTTP/2에 대 한 ASP.NET Core 2.2 추가 지원 합니다. 성능 우선 인 경우에 ASP.NET Core의 현재 버전으로 업그레이드 하는 것이 좋습니다.

<!-- TODO review link and taking advantage of new [performance features](#TBD)
Maybe skip this TBD link as each version will have perf improvements -->

## <a name="minimize-exceptions"></a>예외를 최소화 합니다.

예외는 거의 발생 하지 않아야 합니다. 예외의 throw 및 catch 다른 코드 흐름 패턴을 기준으로 느립니다. 이 인해 일반 프로그램 흐름 제어를 예외를 사용 하지 않아야 합니다.

권장 사항:

* **하지** 사용 하 여 throw 되거나 특히 일반 프로그램 흐름 수단으로 예외를 catch [핫 코드 경로](#hot)합니다.
* **수행할** 를 감지 하 여 예외를 발생 시키는 조건을 처리할 앱에 논리를 포함 합니다.
* **수행할** throw 또는 비정상적인 또는 예기치 않은 조건에 대 한 예외를 catch 합니다.

Application Insights와 같은 앱의 진단 도구, 성능에 영향을 줄 수 있는 앱의 일반적인 예외를 식별에 도움이 됩니다.
