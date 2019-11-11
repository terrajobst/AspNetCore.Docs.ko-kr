---
title: ASP.NET Core의 상태 검사
author: guardrex
description: 앱 및 데이터베이스와 같은 ASP.NET Core 인프라의 상태 검사를 설정하는 방법에 대해 알아봅니다.
monikerRange: '>= aspnetcore-2.2'
ms.author: riande
ms.custom: mvc
ms.date: 11/03/2019
uid: host-and-deploy/health-checks
ms.openlocfilehash: c7cf1c432d2186f0e2f9f5082e8a2229d8a5ef8f
ms.sourcegitcommit: 9e85c2562df5e108d7933635c830297f484bb775
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73463023"
---
# <a name="health-checks-in-aspnet-core"></a><span data-ttu-id="06785-103">ASP.NET Core의 상태 검사</span><span class="sxs-lookup"><span data-stu-id="06785-103">Health checks in ASP.NET Core</span></span>

<span data-ttu-id="06785-104">[Luke Latham](https://github.com/guardrex) 및 [Glenn Condron](https://github.com/glennc) 기준</span><span class="sxs-lookup"><span data-stu-id="06785-104">By [Luke Latham](https://github.com/guardrex) and [Glenn Condron](https://github.com/glennc)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="06785-105">ASP.NET Core는 앱 인프라 구성 요소의 상태를 보고하기 위해 상태 검사 미들웨어와 라이브러리를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-105">ASP.NET Core offers Health Checks Middleware and libraries for reporting the health of app infrastructure components.</span></span>

<span data-ttu-id="06785-106">상태 검사는 앱에 의해 HTTP 엔드포인트로서 노출됩니다.</span><span class="sxs-lookup"><span data-stu-id="06785-106">Health checks are exposed by an app as HTTP endpoints.</span></span> <span data-ttu-id="06785-107">상태 검사 엔드포인트는 다양한 실시간 모니터링 시나리오에 맞게 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="06785-107">Health check endpoints can be configured for a variety of real-time monitoring scenarios:</span></span>

* <span data-ttu-id="06785-108">상태 프로브는 컨테이너 오케스트레이터와 부하 분산 장치가 앱 상태를 검사하는 데 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="06785-108">Health probes can be used by container orchestrators and load balancers to check an app's status.</span></span> <span data-ttu-id="06785-109">예를 들어, 컨테이너 오케스트레이터는 롤링 배포를 중지하거나 컨테이너를 다시 시작하여 실패한 상태 검사에 응답할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="06785-109">For example, a container orchestrator may respond to a failing health check by halting a rolling deployment or restarting a container.</span></span> <span data-ttu-id="06785-110">부하 분산 장치는 장애가 발생한 상태 검사 인스턴스로부터 트래픽을 다른 곳으로 라우팅하여 비정상 앱에 대응할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="06785-110">A load balancer might react to an unhealthy app by routing traffic away from the failing instance to a healthy instance.</span></span>
* <span data-ttu-id="06785-111">메모리, 디스크 및 기타 물리적 서버 리소스의 사용이 정상적인 상태인지 모니터링할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="06785-111">Use of memory, disk, and other physical server resources can be monitored for healthy status.</span></span>
* <span data-ttu-id="06785-112">상태 검사는 데이터베이스 및 외부 서비스 엔드포인트와 같은 앱 종속성을 테스트하여 가용성 및 정상 작동 여부를 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="06785-112">Health checks can test an app's dependencies, such as databases and external service endpoints, to confirm availability and normal functioning.</span></span>

<span data-ttu-id="06785-113">[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/health-checks/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="06785-113">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/health-checks/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="06785-114">샘플 앱에는 이 항목에 설명된 시나리오의 예가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="06785-114">The sample app includes examples of the scenarios described in this topic.</span></span> <span data-ttu-id="06785-115">지정된 시나리오에 대해 샘플 앱을 실행하려면 명령 셸의 프로젝트 폴더에서 [dotnet run](/dotnet/core/tools/dotnet-run) 명령을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-115">To run the sample app for a given scenario, use the [dotnet run](/dotnet/core/tools/dotnet-run) command from the project's folder in a command shell.</span></span> <span data-ttu-id="06785-116">샘플 앱의 사용 방법에 대한 자세한 내용은 샘플 앱의 *README.md* 파일과 이 항목의 시나리오 설명을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="06785-116">See the sample app's *README.md* file and the scenario descriptions in this topic for details on how to use the sample app.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="06785-117">전제 조건</span><span class="sxs-lookup"><span data-stu-id="06785-117">Prerequisites</span></span>

<span data-ttu-id="06785-118">상태 검사는 일반적으로 외부 모니터링 서비스 또는 컨테이너 오케스트레이터와 함께 사용되어 앱 상태를 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-118">Health checks are usually used with an external monitoring service or container orchestrator to check the status of an app.</span></span> <span data-ttu-id="06785-119">앱에 상태 검사를 추가하기 전에 사용할 모니터링 시스템을 결정합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-119">Before adding health checks to an app, decide on which monitoring system to use.</span></span> <span data-ttu-id="06785-120">모니터링 시스템은 어떤 유형의 상태 검사를 만들고 그 엔드포인트를 구성하는 방법을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-120">The monitoring system dictates what types of health checks to create and how to configure their endpoints.</span></span>

<span data-ttu-id="06785-121">[Microsoft.AspNetCore.Diagnostics.HealthChecks](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.HealthChecks) 패키지는 ASP.NET Core 앱에 대해 암시적으로 참조됩니다.</span><span class="sxs-lookup"><span data-stu-id="06785-121">The [Microsoft.AspNetCore.Diagnostics.HealthChecks](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.HealthChecks) package is referenced implicitly for ASP.NET Core apps.</span></span> <span data-ttu-id="06785-122">Entity Framework Core를 사용하여 상태 검사를 수행하려면 [Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore) 패키지에 패키지 참조를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-122">To perform health checks using Entity Framework Core, add a package reference to the [Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore) package.</span></span>

<span data-ttu-id="06785-123">샘플 앱은 여러 시나리오의 상태 검사를 보여주는 시작 코드를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-123">The sample app provides startup code to demonstrate health checks for several scenarios.</span></span> <span data-ttu-id="06785-124">[데이터베이스 프로브](#database-probe) 시나리오는 [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks)를 사용하여 데이터베이스 연결의 상태를 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-124">The [database probe](#database-probe) scenario checks the health of a database connection using [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks).</span></span> <span data-ttu-id="06785-125">[DbContext 프로브](#entity-framework-core-dbcontext-probe) 시나리오는 EF Core `DbContext`를 사용하여 데이터베이스를 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-125">The [DbContext probe](#entity-framework-core-dbcontext-probe) scenario checks a database using an EF Core `DbContext`.</span></span> <span data-ttu-id="06785-126">데이터베이스 시나리오를 탐색하려면 샘플 앱:</span><span class="sxs-lookup"><span data-stu-id="06785-126">To explore the database scenarios, the sample app:</span></span>

* <span data-ttu-id="06785-127">데이터베이스를 만들고 *appsettings.json* 파일에서 연결 문자열을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-127">Creates a database and provides its connection string in the *appsettings.json* file.</span></span>
* <span data-ttu-id="06785-128">해당 프로젝트 파일에 다음 패키지 참조가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="06785-128">Has the following package references in its project file:</span></span>
  * [<span data-ttu-id="06785-129">AspNetCore.HealthChecks.SqlServer</span><span class="sxs-lookup"><span data-stu-id="06785-129">AspNetCore.HealthChecks.SqlServer</span></span>](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/)
  * [<span data-ttu-id="06785-130">Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="06785-130">Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/)

> [!NOTE]
> <span data-ttu-id="06785-131">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks)는 Microsoft에서 유지 관리하거나 지원하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="06785-131">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

<span data-ttu-id="06785-132">다른 상태 검사 시나리오는 관리 포트에 대해 상태 검사를 필터링하는 방법을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="06785-132">Another health check scenario demonstrates how to filter health checks to a management port.</span></span> <span data-ttu-id="06785-133">샘플 앱을 사용하려면 관리 URL과 관리 포트가 포함된 *Properties/launchSettings.json*  파일을 만들어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-133">The sample app requires you to create a *Properties/launchSettings.json* file that includes the management URL and management port.</span></span> <span data-ttu-id="06785-134">자세한 내용은 [포트별 필터링](#filter-by-port) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="06785-134">For more information, see the [Filter by port](#filter-by-port) section.</span></span>

## <a name="basic-health-probe"></a><span data-ttu-id="06785-135">기본 상태 프로브</span><span class="sxs-lookup"><span data-stu-id="06785-135">Basic health probe</span></span>

<span data-ttu-id="06785-136">많은 앱의 경우, 요청을 처리할 수 있는 앱의 가용성(*활동성*)을 보고하는 기본 상태 검사 구성으로 앱 상태를 충분히 파악할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="06785-136">For many apps, a basic health probe configuration that reports the app's availability to process requests (*liveness*) is sufficient to discover the status of the app.</span></span>

<span data-ttu-id="06785-137">기본 구성은 상태 검사 서비스를 등록하고 상태 검사 미들웨어를 호출하여 URL 엔드포인트에서 상태 응답을 사용하여 응답합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-137">The basic configuration registers health check services and calls the Health Checks Middleware to respond at a URL endpoint with a health response.</span></span> <span data-ttu-id="06785-138">기본적으로 특정 종속성이나 하위 시스템을 테스트하기 위해 특정 상태 검사가 등록되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="06785-138">By default, no specific health checks are registered to test any particular dependency or subsystem.</span></span> <span data-ttu-id="06785-139">상태 엔드포인트 URL에서 응답할 수 있는 경우 앱 상태가 좋은 것으로 간주됩니다.</span><span class="sxs-lookup"><span data-stu-id="06785-139">The app is considered healthy if it's capable of responding at the health endpoint URL.</span></span> <span data-ttu-id="06785-140">기본 응답 기록기는 상태(<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>)를 일반 텍스트 응답으로 다시 클라이언트에 기록하여 [HealthStatus.Healthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus), [HealthStatus.Degraded](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) 또는 [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) 상태를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="06785-140">The default response writer writes the status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) as a plaintext response back to the client, indicating either a [HealthStatus.Healthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus), [HealthStatus.Degraded](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) or [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) status.</span></span>

<span data-ttu-id="06785-141">`Startup.ConfigureServices`의 <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*>에 상태 검사 서비스를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-141">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="06785-142">`Startup.Configure`에서 `MapHealthChecks`를 호출하여 상태 검사 엔드포인트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="06785-142">Create a health check endpoint by calling `MapHealthChecks` in `Startup.Configure`.</span></span>

<span data-ttu-id="06785-143">샘플 앱에서 상태 검사 엔드포인트는 `/health`(*BasicStartup.cs*)에 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="06785-143">In the sample app, the health check endpoint is created at `/health` (*BasicStartup.cs*):</span></span>

```csharp
public class BasicStartup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddHealthChecks();
    }

    public void Configure(IApplicationBuilder app)
    {
        app.UseRouting();

        app.UseEndpoints(endpoints =>
        {
            endpoints.MapHealthChecks("/health");
        });
    }
}
```

<span data-ttu-id="06785-144">샘플 앱을 사용하여 기본 구성 시나리오를 실행하려면 명령 셸의 프로젝트 폴더에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-144">To run the basic configuration scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario basic
```

### <a name="docker-example"></a><span data-ttu-id="06785-145">Docker 예</span><span class="sxs-lookup"><span data-stu-id="06785-145">Docker example</span></span>

<span data-ttu-id="06785-146">[Docker](xref:host-and-deploy/docker/index)는 기본 상태 검사 구성을 사용하는 앱의 상태를 검사하는 데 사용할 수 있는 기본 제공 `HEALTHCHECK` 지시문을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-146">[Docker](xref:host-and-deploy/docker/index) offers a built-in `HEALTHCHECK` directive that can be used to check the status of an app that uses the basic health check configuration:</span></span>

```
HEALTHCHECK CMD curl --fail http://localhost:5000/health || exit
```

## <a name="create-health-checks"></a><span data-ttu-id="06785-147">상태 검사 만들기</span><span class="sxs-lookup"><span data-stu-id="06785-147">Create health checks</span></span>

<span data-ttu-id="06785-148">상태 검사는 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> 인터페이스를 구현하여 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="06785-148">Health checks are created by implementing the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> interface.</span></span> <span data-ttu-id="06785-149"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*> 메서드는 상태를 `Healthy`, `Degraded` 또는 `Unhealthy`로 나타내는 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult>를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-149">The <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*> method returns a <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> that indicates the health as `Healthy`, `Degraded`, or `Unhealthy`.</span></span> <span data-ttu-id="06785-150">결과는 구성 가능한 상태 코드가 있는 일반 텍스트 응답으로 기록됩니다(구성은 [상태 검사 옵션](#health-check-options) 섹션에 설명되어 있음).</span><span class="sxs-lookup"><span data-stu-id="06785-150">The result is written as a plaintext response with a configurable status code (configuration is described in the [Health check options](#health-check-options) section).</span></span> <span data-ttu-id="06785-151"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult>는 선택 사항인 키-값 쌍을 반환할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="06785-151"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> can also return optional key-value pairs.</span></span>

<span data-ttu-id="06785-152">다음 `ExampleHealthCheck` 클래스는 상태 검사의 레이아웃을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="06785-152">The following `ExampleHealthCheck` class demonstrates the layout of a health check.</span></span> <span data-ttu-id="06785-153">상태 검사 논리는 `CheckHealthAsync` 메서드에 배치됩니다.</span><span class="sxs-lookup"><span data-stu-id="06785-153">The health checks logic is placed in the `CheckHealthAsync` method.</span></span> <span data-ttu-id="06785-154">다음 예제에서는 더미 변수 `healthCheckResultHealthy`를 `true`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-154">The following example sets a dummy variable, `healthCheckResultHealthy`, to `true`.</span></span> <span data-ttu-id="06785-155">`healthCheckResultHealthy` 값이 `false`로 설정되면 [HealthCheckResult.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult.Unhealthy*) 상태가 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="06785-155">If the value of `healthCheckResultHealthy` is set to `false`, the [HealthCheckResult.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult.Unhealthy*) status is returned.</span></span>

```csharp
public class ExampleHealthCheck : IHealthCheck
{
    public Task<HealthCheckResult> CheckHealthAsync(
        HealthCheckContext context,
        CancellationToken cancellationToken = default(CancellationToken))
    {
        var healthCheckResultHealthy = true;

        if (healthCheckResultHealthy)
        {
            return Task.FromResult(
                HealthCheckResult.Healthy("A healthy result."));
        }

        return Task.FromResult(
            HealthCheckResult.Unhealthy("An unhealthy result."));
    }
}
```

## <a name="register-health-check-services"></a><span data-ttu-id="06785-156">상태 검사 서비스 등록</span><span class="sxs-lookup"><span data-stu-id="06785-156">Register health check services</span></span>

<span data-ttu-id="06785-157">`ExampleHealthCheck` 형식은 `Startup.ConfigureServices`의 <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>으로 상태 검사 서비스에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="06785-157">The `ExampleHealthCheck` type is added to health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck<ExampleHealthCheck>("example_health_check");
```

<span data-ttu-id="06785-158">다음 예제에 표시된 <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> 오버로드는 상태 검사에서 오류가 보고되면 보고하도록 오류 상태(<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>)를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-158">The <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> overload shown in the following example sets the failure status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) to report when the health check reports a failure.</span></span> <span data-ttu-id="06785-159">오류 상태가 `null`(기본값)로 설정되면 [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus)가 보고됩니다.</span><span class="sxs-lookup"><span data-stu-id="06785-159">If the failure status is set to `null` (default), [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) is reported.</span></span> <span data-ttu-id="06785-160">이 오버로드는 라이브러리 작성자에게 유용한 시나리오입니다. 이 라이브러리에서는 상태 검사 구현이 설정을 준수하는 경우 상태 검사 실패가 발생할 때 라이브러리가 나타내는 오류 상태는 앱에 의해 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="06785-160">This overload is a useful scenario for library authors, where the failure status indicated by the library is enforced by the app when a health check failure occurs if the health check implementation honors the setting.</span></span>

<span data-ttu-id="06785-161">*태그*를 사용하여 상태 검사를 필터링할 수 있습니다([필터 상태 검사](#filter-health-checks) 섹션에 자세히 설명되어 있음).</span><span class="sxs-lookup"><span data-stu-id="06785-161">*Tags* can be used to filter health checks (described further in the [Filter health checks](#filter-health-checks) section).</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck<ExampleHealthCheck>(
        "example_health_check",
        failureStatus: HealthStatus.Degraded,
        tags: new[] { "example" });
```

<span data-ttu-id="06785-162"><xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>는 람다 함수를 실행할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="06785-162"><xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> can also execute a lambda function.</span></span> <span data-ttu-id="06785-163">다음 예제에서 상태 검사 이름은 `Example`로 지정되고 검사는 항상 정상 상태를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-163">In the following example, the health check name is specified as `Example` and the check always returns a healthy state:</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck("Example", () =>
        HealthCheckResult.Healthy("Example is OK!"), tags: new[] { "example" });
```

## <a name="use-health-checks-routing"></a><span data-ttu-id="06785-164">상태 검사 라우팅 사용</span><span class="sxs-lookup"><span data-stu-id="06785-164">Use Health Checks Routing</span></span>

<span data-ttu-id="06785-165">`Startup.Configure`에서 엔드포인트 URL 또는 상대 경로를 사용하여 엔드포인트 작성기에 `MapHealthChecks`를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-165">In `Startup.Configure`, call `MapHealthChecks` on the endpoint builder with the endpoint URL or relative path:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health");
});
```

### <a name="require-host"></a><span data-ttu-id="06785-166">호스트 필요</span><span class="sxs-lookup"><span data-stu-id="06785-166">Require host</span></span>

<span data-ttu-id="06785-167">`RequireHost`를 호출하여 상태 검사 엔드포인트에 대해 허용된 호스트를 하나 이상 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-167">Call `RequireHost` to specify one or more permitted hosts for the health check endpoint.</span></span> <span data-ttu-id="06785-168">호스트는 punycode가 아닌 유니코드여야 하고 포트를 포함할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="06785-168">Hosts should be Unicode rather than punycode and may include a port.</span></span> <span data-ttu-id="06785-169">컬렉션을 제공하지 않으면 모든 호스트가 허용됩니다.</span><span class="sxs-lookup"><span data-stu-id="06785-169">If a collection isn't supplied, any host is accepted.</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health").RequireHost("www.contoso.com:5001");
});
```

<span data-ttu-id="06785-170">자세한 내용은 [포트별 필터링](#filter-by-port) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="06785-170">For more information, see the [Filter by port](#filter-by-port) section.</span></span>

### <a name="require-authorization"></a><span data-ttu-id="06785-171">권한 부여 필요</span><span class="sxs-lookup"><span data-stu-id="06785-171">Require authorization</span></span>

<span data-ttu-id="06785-172">`RequireAuthorization`을 호출하여 상태 검사 요청 엔드포인트에서 권한 부여 미들웨어를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-172">Call `RequireAuthorization` to run Authorization Middleware on the health check request endpoint.</span></span> <span data-ttu-id="06785-173">`RequireAuthorization` 오버로드는 하나 이상의 권한 부여 정책을 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-173">A `RequireAuthorization` overload accepts one or more authorization policies.</span></span> <span data-ttu-id="06785-174">정책을 제공하지 않으면 기본 권한 부여 정책이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="06785-174">If a policy isn't provided, the default authorization policy is used.</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health").RequireAuthorization();
});
```

### <a name="enable-cross-origin-requests-cors"></a><span data-ttu-id="06785-175">원본 간 요청(CORS) 사용</span><span class="sxs-lookup"><span data-stu-id="06785-175">Enable Cross-Origin Requests (CORS)</span></span>

<span data-ttu-id="06785-176">브라우저에서 수동으로 상태 검사를 수행하는 것이 일반적이지는 않지만, 상태 검사 엔드포인트에 `RequireCors`를 호출하여 CORS 미들웨어를 사용하도록 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="06785-176">Although performing health checks manually from a browser isn't a common use scenario, CORS Middleware can be enabled by calling `RequireCors` on health checks endpoints.</span></span> <span data-ttu-id="06785-177">`RequireCors` 오버로드는 CORS 정책 작성기 대리자(`CorsPolicyBuilder`) 또는 정책 이름을 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-177">A `RequireCors` overload accepts a CORS policy builder delegate (`CorsPolicyBuilder`) or a policy name.</span></span> <span data-ttu-id="06785-178">정책을 제공하지 않으면 기본 CORS 정책이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="06785-178">If a policy isn't provided, the default CORS policy is used.</span></span> <span data-ttu-id="06785-179">자세한 내용은 <xref:security/cors>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="06785-179">For more information, see <xref:security/cors>.</span></span>

## <a name="health-check-options"></a><span data-ttu-id="06785-180">상태 검사 옵션</span><span class="sxs-lookup"><span data-stu-id="06785-180">Health check options</span></span>

<span data-ttu-id="06785-181"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions>는 다음과 같은 상태 검사 동작을 사용자 지정할 수 있는 기회를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-181"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions> provide an opportunity to customize health check behavior:</span></span>

* [<span data-ttu-id="06785-182">상태 검사 필터링</span><span class="sxs-lookup"><span data-stu-id="06785-182">Filter health checks</span></span>](#filter-health-checks)
* [<span data-ttu-id="06785-183">HTTP 상태 코드 사용자 지정</span><span class="sxs-lookup"><span data-stu-id="06785-183">Customize the HTTP status code</span></span>](#customize-the-http-status-code)
* [<span data-ttu-id="06785-184">캐시 헤더 표시 안 함</span><span class="sxs-lookup"><span data-stu-id="06785-184">Suppress cache headers</span></span>](#suppress-cache-headers)
* [<span data-ttu-id="06785-185">출력 사용자 지정</span><span class="sxs-lookup"><span data-stu-id="06785-185">Customize output</span></span>](#customize-output)

### <a name="filter-health-checks"></a><span data-ttu-id="06785-186">상태 검사 필터링</span><span class="sxs-lookup"><span data-stu-id="06785-186">Filter health checks</span></span>

<span data-ttu-id="06785-187">기본적으로 상태 검사 미들웨어는 등록된 모든 상태 검사를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-187">By default, Health Checks Middleware runs all registered health checks.</span></span> <span data-ttu-id="06785-188">상태 검사 하위 세트를 실행하려면 <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate> 옵션에 부울 값을 반환하는 함수를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-188">To run a subset of health checks, provide a function that returns a boolean to the <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate> option.</span></span> <span data-ttu-id="06785-189">다음 예제에서는 상태 검사의 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.Tags> 속성이 `foo_tag` 또는 `baz_tag`와 일치하는 경우에만 `true`가 반환되는 함수의 조건문에서 태그(`bar_tag`)에 의해 `Bar` 상태 검사가 필터링됩니다.</span><span class="sxs-lookup"><span data-stu-id="06785-189">In the following example, the `Bar` health check is filtered out by its tag (`bar_tag`) in the function's conditional statement, where `true` is only returned if the health check's <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.Tags> property matches `foo_tag` or `baz_tag`:</span></span>

<span data-ttu-id="06785-190">`Startup.ConfigureServices`의 경우</span><span class="sxs-lookup"><span data-stu-id="06785-190">In `Startup.ConfigureServices`:</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck("Foo", () =>
        HealthCheckResult.Healthy("Foo is OK!"), tags: new[] { "foo_tag" })
    .AddCheck("Bar", () =>
        HealthCheckResult.Unhealthy("Bar is unhealthy!"), tags: new[] { "bar_tag" })
    .AddCheck("Baz", () =>
        HealthCheckResult.Healthy("Baz is OK!"), tags: new[] { "baz_tag" });
```

<span data-ttu-id="06785-191">`Startup.Configure`에서 `Predicate`는 'Bar' 상태 검사를 필터링합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-191">In `Startup.Configure`, the `Predicate` filters out the 'Bar' health check.</span></span> <span data-ttu-id="06785-192">Foo 및 Baz만 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="06785-192">Only Foo and Baz execute.:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health", new HealthCheckOptions()
    {
        Predicate = (check) => check.Tags.Contains("foo_tag") ||
            check.Tags.Contains("baz_tag")
    });
});
```

### <a name="customize-the-http-status-code"></a><span data-ttu-id="06785-193">HTTP 상태 코드 사용자 지정</span><span class="sxs-lookup"><span data-stu-id="06785-193">Customize the HTTP status code</span></span>

<span data-ttu-id="06785-194"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResultStatusCodes>를 사용하여 상태를 HTTP 상태 코드에 매핑하는 작업을 사용자 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="06785-194">Use <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResultStatusCodes> to customize the mapping of health status to HTTP status codes.</span></span> <span data-ttu-id="06785-195">다음 <xref:Microsoft.AspNetCore.Http.StatusCodes> 할당은 미들웨어에서 사용되는 기본값입니다.</span><span class="sxs-lookup"><span data-stu-id="06785-195">The following <xref:Microsoft.AspNetCore.Http.StatusCodes> assignments are the default values used by the middleware.</span></span> <span data-ttu-id="06785-196">요구 사항에 맞게 상태 코드 값을 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-196">Change the status code values to meet your requirements.</span></span>

<span data-ttu-id="06785-197">`Startup.Configure`의 경우</span><span class="sxs-lookup"><span data-stu-id="06785-197">In `Startup.Configure`:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health", new HealthCheckOptions()
    {
        ResultStatusCodes =
        {
            [HealthStatus.Healthy] = StatusCodes.Status200OK,
            [HealthStatus.Degraded] = StatusCodes.Status200OK,
            [HealthStatus.Unhealthy] = StatusCodes.Status503ServiceUnavailable
        }
    });
});
```

### <a name="suppress-cache-headers"></a><span data-ttu-id="06785-198">캐시 헤더 표시 안 함</span><span class="sxs-lookup"><span data-stu-id="06785-198">Suppress cache headers</span></span>

<span data-ttu-id="06785-199"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.AllowCachingResponses>는 응답 캐싱을 방지하기 위해 상태 검사 미들웨어가 HTTP 헤더를 프로브 응답에 추가할지 여부를 제어합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-199"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.AllowCachingResponses> controls whether the Health Checks Middleware adds HTTP headers to a probe response to prevent response caching.</span></span> <span data-ttu-id="06785-200">값이 `false`(기본값)인 경우 미들웨어는 응답 캐싱을 방지하기 위해 `Cache-Control`, `Expires` 및 `Pragma` 헤더를 설정하거나 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-200">If the value is `false` (default), the middleware sets or overrides the `Cache-Control`, `Expires`, and `Pragma` headers to prevent response caching.</span></span> <span data-ttu-id="06785-201">값이 `true`인 경우 미들웨어는 응답 캐시 헤더를 수정하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="06785-201">If the value is `true`, the middleware doesn't modify the cache headers of the response.</span></span>

<span data-ttu-id="06785-202">`Startup.Configure`의 경우</span><span class="sxs-lookup"><span data-stu-id="06785-202">In `Startup.Configure`:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health", new HealthCheckOptions()
    {
        AllowCachingResponses = false
    });
});
```

### <a name="customize-output"></a><span data-ttu-id="06785-203">출력 사용자 지정</span><span class="sxs-lookup"><span data-stu-id="06785-203">Customize output</span></span>

<span data-ttu-id="06785-204"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter> 옵션은 응답을 기록하는 데 사용되는 대리자를 가져오거나 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-204">The <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter> option gets or sets a delegate used to write the response.</span></span>

<span data-ttu-id="06785-205">`Startup.Configure`의 경우</span><span class="sxs-lookup"><span data-stu-id="06785-205">In `Startup.Configure`:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health", new HealthCheckOptions()
    {
        ResponseWriter = WriteResponse
    });
});
```

<span data-ttu-id="06785-206">기본 대리자는 [HealthReport.Status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status)의 문자열 값을 사용하여 최소 일반 텍스트 응답을 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-206">The default delegate writes a minimal plaintext response with the string value of [HealthReport.Status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status).</span></span> <span data-ttu-id="06785-207">다음 사용자 지정 대리자 `WriteResponse`는 사용자 지정 JSON 응답을 출력합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-207">The following custom delegate, `WriteResponse`, outputs a custom JSON response:</span></span>

```csharp
private static Task WriteResponse(HttpContext httpContext, HealthReport result)
{
    httpContext.Response.ContentType = "application/json";

    var json = new JObject(
        new JProperty("status", result.Status.ToString()),
        new JProperty("results", new JObject(result.Entries.Select(pair =>
            new JProperty(pair.Key, new JObject(
                new JProperty("status", pair.Value.Status.ToString()),
                new JProperty("description", pair.Value.Description),
                new JProperty("data", new JObject(pair.Value.Data.Select(
                    p => new JProperty(p.Key, p.Value))))))))));
    return httpContext.Response.WriteAsync(
        json.ToString(Formatting.Indented));
}
```

<span data-ttu-id="06785-208">상태 검사 시스템은 사용자가 선택한 모니터링 시스템에만 적용되는 형식이므로 복합 JSON 반환 형식에 대한 기본 제공 지원을 제공하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="06785-208">The health checks system doesn't provide built-in support for complex JSON return formats because the format is specific to your choice of monitoring system.</span></span> <span data-ttu-id="06785-209">필요에 따라 앞의 예제에서 `JObject`를 자유롭게 사용자 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="06785-209">Feel free to customize the `JObject` in the preceding example as necessary to meet your needs.</span></span>

## <a name="database-probe"></a><span data-ttu-id="06785-210">데이터베이스 프로브</span><span class="sxs-lookup"><span data-stu-id="06785-210">Database probe</span></span>

<span data-ttu-id="06785-211">상태 검사는 데이터베이스 쿼리를 지정하여 데이터베이스가 정상적으로 응답하는지를 나타내기 위해 부울 테스트로서 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="06785-211">A health check can specify a database query to run as a boolean test to indicate if the database is responding normally.</span></span>

<span data-ttu-id="06785-212">샘플 앱은 ASP.NET Core 앱의 상태 검사 라이브러리인 [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks)를 사용하여 SQL Server 데이터베이스에 대한 상태 검사를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-212">The sample app uses [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks), a health check library for ASP.NET Core apps, to perform a health check on a SQL Server database.</span></span> <span data-ttu-id="06785-213">`AspNetCore.Diagnostics.HealthChecks`는 데이터베이스에 연결이 양호한지 확인하기 위해 데이터베이스에 대해 `SELECT 1` 쿼리를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-213">`AspNetCore.Diagnostics.HealthChecks` executes a `SELECT 1` query against the database to confirm the connection to the database is healthy.</span></span>

> [!WARNING]
> <span data-ttu-id="06785-214">쿼리로 데이터베이스 연결을 검사할 때 신속하게 반환되는 쿼리를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-214">When checking a database connection with a query, choose a query that returns quickly.</span></span> <span data-ttu-id="06785-215">쿼리 방식 실행은 데이터베이스의 오버로드와 성능 저하를 유발할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="06785-215">The query approach runs the risk of overloading the database and degrading its performance.</span></span> <span data-ttu-id="06785-216">대부분의 경우 테스트 쿼리를 실행할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="06785-216">In most cases, running a test query isn't necessary.</span></span> <span data-ttu-id="06785-217">간단히 데이터베이스에 연결하는 정도로도 충분합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-217">Merely making a successful connection to the database is sufficient.</span></span> <span data-ttu-id="06785-218">쿼리를 실행해야 하는 경우 `SELECT 1`과 같은 간단한 SELECT 쿼리를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-218">If you find it necessary to run a query, choose a simple SELECT query, such as `SELECT 1`.</span></span>

<span data-ttu-id="06785-219">[AspNetCore.HealthChecks.SqlServer](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/)에 대한 패키지 참조를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-219">Include a package reference to [AspNetCore.HealthChecks.SqlServer](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/).</span></span>

<span data-ttu-id="06785-220">샘플 앱의 *appsettings.json* 파일에서 유효한 데이터베이스 연결 문자열을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-220">Supply a valid database connection string in the *appsettings.json* file of the sample app.</span></span> <span data-ttu-id="06785-221">앱은 `HealthCheckSample`이라고 하는 SQL Server 데이터베이스를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-221">The app uses a SQL Server database named `HealthCheckSample`:</span></span>

[!code-json[](health-checks/samples/3.x/HealthChecksSample/appsettings.json?highlight=3)]

<span data-ttu-id="06785-222">`Startup.ConfigureServices`의 <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*>에 상태 검사 서비스를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-222">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="06785-223">샘플 앱은 데이터베이스의 연결 문자열(*DbHealthStartup.cs*)을 사용하여 `AddSqlServer` 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-223">The sample app calls the `AddSqlServer` method with the database's connection string (*DbHealthStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/DbHealthStartup.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="06785-224">상태 검사 엔드포인트는 `Startup.Configure`에서 `MapHealthChecks`를 호출하여 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="06785-224">A health check endpoint is created by calling `MapHealthChecks` in `Startup.Configure`:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health");
}
```

<span data-ttu-id="06785-225">샘플 앱을 사용하여 데이터베이스 프로브 시나리오를 실행하려면 명령 셸의 프로젝트 폴더에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-225">To run the database probe scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario db
```

> [!NOTE]
> <span data-ttu-id="06785-226">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks)는 Microsoft에서 유지 관리하거나 지원하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="06785-226">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

## <a name="entity-framework-core-dbcontext-probe"></a><span data-ttu-id="06785-227">Entity Framework Core DbContext 프로브</span><span class="sxs-lookup"><span data-stu-id="06785-227">Entity Framework Core DbContext probe</span></span>

<span data-ttu-id="06785-228">`DbContext` 검사는 앱이 EF Core `DbContext`에 대해 구성된 데이터베이스와 통신할 수 있음을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-228">The `DbContext` check confirms that the app can communicate with the database configured for an EF Core `DbContext`.</span></span> <span data-ttu-id="06785-229">`DbContext` 검사는 다음과 같은 앱에서 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="06785-229">The `DbContext` check is supported in apps that:</span></span>

* <span data-ttu-id="06785-230">[EF(Entity Framework) Core](/ef/core/)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-230">Use [Entity Framework (EF) Core](/ef/core/).</span></span>
* <span data-ttu-id="06785-231">[Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/)에 대한 패키지 참조를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-231">Include a package reference to [Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/).</span></span>

<span data-ttu-id="06785-232">`AddDbContextCheck<TContext>`는 `DbContext`에 대한 상태 검사를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-232">`AddDbContextCheck<TContext>` registers a health check for a `DbContext`.</span></span> <span data-ttu-id="06785-233">`DbContext`는 메서드에 `TContext`로 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="06785-233">The `DbContext` is supplied as the `TContext` to the method.</span></span> <span data-ttu-id="06785-234">오버로드는 오류 상태, 태그 및 사용자 지정 테스트 쿼리를 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="06785-234">An overload is available to configure the failure status, tags, and a custom test query.</span></span>

<span data-ttu-id="06785-235">기본적으로 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="06785-235">By default:</span></span>

* <span data-ttu-id="06785-236">`DbContextHealthCheck`는 EF Core의 `CanConnectAsync` 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-236">The `DbContextHealthCheck` calls EF Core's `CanConnectAsync` method.</span></span> <span data-ttu-id="06785-237">`AddDbContextCheck` 메서드 오버로드를 사용하여 상태를 검사할 때 실행되는 작업을 사용자 정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="06785-237">You can customize what operation is run when checking health using `AddDbContextCheck` method overloads.</span></span>
* <span data-ttu-id="06785-238">상태 검사의 이름은 `TContext` 형식의 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="06785-238">The name of the health check is the name of the `TContext` type.</span></span>

<span data-ttu-id="06785-239">샘플 앱에서 `AppDbContext`는 `AddDbContextCheck`에 제공되고 `Startup.ConfigureServices`의 서비스로서 등록됩니다(*DbContextHealthStartup.cs*).</span><span class="sxs-lookup"><span data-stu-id="06785-239">In the sample app, `AppDbContext` is provided to `AddDbContextCheck` and registered as a service in `Startup.ConfigureServices` (*DbContextHealthStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/DbContextHealthStartup.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="06785-240">상태 검사 엔드포인트는 `Startup.Configure`에서 `MapHealthChecks`를 호출하여 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="06785-240">A health check endpoint is created by calling `MapHealthChecks` in `Startup.Configure`:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health");
}
```

<span data-ttu-id="06785-241">샘플 앱을 사용하여 `DbContext` 프로브 시나리오를 실행하려면 연결 문자열로 지정된 데이터베이스가 SQL Server 인스턴스에 있지 않은지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-241">To run the `DbContext` probe scenario using the sample app, confirm that the database specified by the connection string doesn't exist in the SQL Server instance.</span></span> <span data-ttu-id="06785-242">데이터베이스가 있으면 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-242">If the database exists, delete it.</span></span>

<span data-ttu-id="06785-243">명령 셸의 프로젝트 폴더에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-243">Execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario dbcontext
```

<span data-ttu-id="06785-244">앱을 실행한 후 브라우저의 `/health` 엔드포인트에 요청하여 상태를 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-244">After the app is running, check the health status by making a request to the `/health` endpoint in a browser.</span></span> <span data-ttu-id="06785-245">데이터베이스 및 `AppDbContext`가 존재하기 않기 때문에 앱은 다음 응답을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-245">The database and `AppDbContext` don't exist, so app provides the following response:</span></span>

```
Unhealthy
```

<span data-ttu-id="06785-246">데이터베이스를 만드는 샘플 앱을 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-246">Trigger the sample app to create the database.</span></span> <span data-ttu-id="06785-247">`/createdatabase`에 요청합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-247">Make a request to `/createdatabase`.</span></span> <span data-ttu-id="06785-248">앱은 다음과 같이 응답합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-248">The app responds:</span></span>

```
Creating the database...
Done!
Navigate to /health to see the health status.
```

<span data-ttu-id="06785-249">`/health` 엔드포인트에 요청합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-249">Make a request to the `/health` endpoint.</span></span> <span data-ttu-id="06785-250">데이터베이스와 컨텍스트가 존재하기 때문에 앱은 다음과 같이 응답합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-250">The database and context exist, so app responds:</span></span>

```
Healthy
```

<span data-ttu-id="06785-251">데이터베이스를 삭제하는 샘플 앱을 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-251">Trigger the sample app to delete the database.</span></span> <span data-ttu-id="06785-252">`/deletedatabase`에 요청합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-252">Make a request to `/deletedatabase`.</span></span> <span data-ttu-id="06785-253">앱은 다음과 같이 응답합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-253">The app responds:</span></span>

```
Deleting the database...
Done!
Navigate to /health to see the health status.
```

<span data-ttu-id="06785-254">`/health` 엔드포인트에 요청합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-254">Make a request to the `/health` endpoint.</span></span> <span data-ttu-id="06785-255">앱은 다음과 같이 비정상 응답을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-255">The app provides an unhealthy response:</span></span>

```
Unhealthy
```

## <a name="separate-readiness-and-liveness-probes"></a><span data-ttu-id="06785-256">별도의 준비 상태 및 활동성 프로브</span><span class="sxs-lookup"><span data-stu-id="06785-256">Separate readiness and liveness probes</span></span>

<span data-ttu-id="06785-257">일부 호스팅 시나리오에서 두 개의 앱 상태를 구분하는 한 쌍의 상태 검사가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="06785-257">In some hosting scenarios, a pair of health checks are used that distinguish two app states:</span></span>

* <span data-ttu-id="06785-258">앱은 작동하지만, 아직 요청을 받을 준비가 되지 않았습니다.</span><span class="sxs-lookup"><span data-stu-id="06785-258">The app is functioning but not yet ready to receive requests.</span></span> <span data-ttu-id="06785-259">이 상태는 앱의 *준비 상태*입니다.</span><span class="sxs-lookup"><span data-stu-id="06785-259">This state is the app's *readiness*.</span></span>
* <span data-ttu-id="06785-260">앱을 작동하고 요청에 응답합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-260">The app is functioning and responding to requests.</span></span> <span data-ttu-id="06785-261">이 상태는 앱의 *활동성*입니다.</span><span class="sxs-lookup"><span data-stu-id="06785-261">This state is the app's *liveness*.</span></span>

<span data-ttu-id="06785-262">준비 상태 검사는 대개 앱의 하위 시스템 및 리소스를 모두 사용할 수 있는지 확인하기 위해 보다 광범위하고 시간이 많이 소요되는 검사 세트를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-262">The readiness check usually performs a more extensive and time-consuming set of checks to determine if all of the app's subsystems and resources are available.</span></span> <span data-ttu-id="06785-263">활동성 검사는 앱이 요청을 처리할 수 있는지 여부를 확인하기 위해 빠른 검사만 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-263">A liveness check merely performs a quick check to determine if the app is available to process requests.</span></span> <span data-ttu-id="06785-264">앱이 준비 상태 검사를 통과한 후에는 값 비싼 준비 상태 검사 세트로 앱에 추가로 부담 지울 필요가 없으며&mdash;이후 활동성에 대한 검사만 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-264">After the app passes its readiness check, there's no need to burden the app further with the expensive set of readiness checks&mdash;further checks only require checking for liveness.</span></span>

<span data-ttu-id="06785-265">샘플 앱에는 [호스팅된 서비스](xref:fundamentals/host/hosted-services)에서 장기 실행 시작 작업 완료를 보고하는 상태 검사가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="06785-265">The sample app contains a health check to report the completion of long-running startup task in a [Hosted Service](xref:fundamentals/host/hosted-services).</span></span> <span data-ttu-id="06785-266">`StartupHostedServiceHealthCheck`는 장기 실행 작업이 완료되면(*StartupHostedServiceHealthCheck.cs*) 호스팅된 서비스를 `true`로 설정할 수 있는 속성 `StartupTaskCompleted`를 노출합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-266">The `StartupHostedServiceHealthCheck` exposes a property, `StartupTaskCompleted`, that the hosted service can set to `true` when its long-running task is finished (*StartupHostedServiceHealthCheck.cs*):</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/StartupHostedServiceHealthCheck.cs?name=snippet1&highlight=7-11)]

<span data-ttu-id="06785-267">장기 실행 백그라운드 작업은 [호스팅된 서비스](xref:fundamentals/host/hosted-services)(*Services/StartupHostedService*)에 의해 시작됩니다.</span><span class="sxs-lookup"><span data-stu-id="06785-267">The long-running background task is started by a [Hosted Service](xref:fundamentals/host/hosted-services) (*Services/StartupHostedService*).</span></span> <span data-ttu-id="06785-268">작업이 끝나면 `StartupHostedServiceHealthCheck.StartupTaskCompleted`는 `true`로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="06785-268">At the conclusion of the task, `StartupHostedServiceHealthCheck.StartupTaskCompleted` is set to `true`:</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/Services/StartupHostedService.cs?name=snippet1&highlight=18-20)]

<span data-ttu-id="06785-269">상태 검사는 호스팅된 서비스와 함께 `Startup.ConfigureServices`의 <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>에 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="06785-269">The health check is registered with <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> in `Startup.ConfigureServices` along with the hosted service.</span></span> <span data-ttu-id="06785-270">호스팅된 서비스가 상태 검사에서 속성을 설정해야 하기 때문에 상태 확인도 서비스 컨테이너(*LivenessProbeStartup.cs*)에 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="06785-270">Because the hosted service must set the property on the health check, the health check is also registered in the service container (*LivenessProbeStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="06785-271">상태 검사 엔드포인트는 `Startup.Configure`에서 `MapHealthChecks`를 호출하여 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="06785-271">A health check endpoint is created by calling `MapHealthChecks` in `Startup.Configure`.</span></span> <span data-ttu-id="06785-272">샘플 앱에서 상태 검사 엔드포인트는 다음 위치에 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="06785-272">In the sample app, the health check endpoints are created at:</span></span>

* <span data-ttu-id="06785-273">준비 상태 검사를 위해 `/health/ready`.</span><span class="sxs-lookup"><span data-stu-id="06785-273">`/health/ready` for the readiness check.</span></span> <span data-ttu-id="06785-274">준비 상태 검사는 `ready` 태그가 있는 상태 검사에서 상태 검사를 필터링합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-274">The readiness check filters health checks to the health check with the `ready` tag.</span></span>
* <span data-ttu-id="06785-275">활동성 검사를 위해 `/health/live`.</span><span class="sxs-lookup"><span data-stu-id="06785-275">`/health/live` for the liveness check.</span></span> <span data-ttu-id="06785-276">활동성 검사는 [HealthCheckOptions.Predicate](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate)에서 `false`를 반환하여 `StartupHostedServiceHealthCheck`를 필터링합니다(자세한 내용은 [상태 검사 필터링](#filter-health-checks) 참조).</span><span class="sxs-lookup"><span data-stu-id="06785-276">The liveness check filters out the `StartupHostedServiceHealthCheck` by returning `false` in the [HealthCheckOptions.Predicate](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate) (for more information, see [Filter health checks](#filter-health-checks))</span></span>

<span data-ttu-id="06785-277">다음 예제 코드에서:</span><span class="sxs-lookup"><span data-stu-id="06785-277">In the following example code:</span></span>

* <span data-ttu-id="06785-278">준비 상태 검사는 'ready' 태그가 있는 등록된 검사를 모두 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-278">The readiness check uses all registered checks with the 'ready' tag.</span></span>
* <span data-ttu-id="06785-279">`Predicate`는 모든 검사를 제외하고 200-Ok를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-279">The `Predicate` excludes all checks and return a 200-Ok.</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health/ready", new HealthCheckOptions()
    {
        Predicate = (check) => check.Tags.Contains("ready"),
    });

    endpoints.MapHealthChecks("/health/live", new HealthCheckOptions()
    {
        Predicate = (_) => false
    });
}
```

<span data-ttu-id="06785-280">샘플 앱을 사용하여 준비 상태/활동성 구성 시나리오를 실행하려면 명령 셸의 프로젝트 폴더에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-280">To run the readiness/liveness configuration scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario liveness
```

<span data-ttu-id="06785-281">브라우저에서 15초가 경과할 때까지 `/health/ready`를 여러 번 방문합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-281">In a browser, visit `/health/ready` several times until 15 seconds have passed.</span></span> <span data-ttu-id="06785-282">상태 검사는 첫 15초 동안 *Unhealthy*를 보고합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-282">The health check reports *Unhealthy* for the first 15 seconds.</span></span> <span data-ttu-id="06785-283">15초 후, 엔드포인트는 *Healthy*를 보고하여 호스트된 서비스에 의해 장기 실행 작업이 완료되었음을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="06785-283">After 15 seconds, the endpoint reports *Healthy*, which reflects the completion of the long-running task by the hosted service.</span></span>

<span data-ttu-id="06785-284">이 예제에서는 2초 지연을 사용하여 첫 번째 준비 검사를 실행하는 상태 검사 게시자(<xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> 구현)도 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="06785-284">This example also creates a Health Check Publisher (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation) that runs the first readiness check with a two second delay.</span></span> <span data-ttu-id="06785-285">자세한 내용은 [상태 검사 게시자](#health-check-publisher) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="06785-285">For more information, see the [Health Check Publisher](#health-check-publisher) section.</span></span>

### <a name="kubernetes-example"></a><span data-ttu-id="06785-286">Kubernetes 예제</span><span class="sxs-lookup"><span data-stu-id="06785-286">Kubernetes example</span></span>

<span data-ttu-id="06785-287">별도의 준비 상태 및 활동성 검사를 사용하는 것은 [Kubernetes](https://kubernetes.io/)와 같은 환경에서 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-287">Using separate readiness and liveness checks is useful in an environment such as [Kubernetes](https://kubernetes.io/).</span></span> <span data-ttu-id="06785-288">Kubernetes에서 앱은 기본 데이터베이스 가용성 테스트와 같이 요청을 수락하기 전에 시간이 많이 걸리는 시작 작업을 수행해야 할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="06785-288">In Kubernetes, an app might be required to perform time-consuming startup work before accepting requests, such as a test of the underlying database availability.</span></span> <span data-ttu-id="06785-289">별도의 검사를 사용하면 오케스트레이터가 앱이 작동하고 있지만 아직 준비가 되지 않았는지 또는 앱이 시작되지 않았는지 구분할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="06785-289">Using separate checks allows the orchestrator to distinguish whether the app is functioning but not yet ready or if the app has failed to start.</span></span> <span data-ttu-id="06785-290">Kubernetes의 준비 상태 및 활동성 프로브에 대한 자세한 내용은 Kubernetes 설명서의 [활동성 및 준비 상태 프로브 구성](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="06785-290">For more information on readiness and liveness probes in Kubernetes, see [Configure Liveness and Readiness Probes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) in the Kubernetes documentation.</span></span>

<span data-ttu-id="06785-291">다음 예제는 Kubernetes 준비 상태 프로브 구성을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="06785-291">The following example demonstrates a Kubernetes readiness probe configuration:</span></span>

```
spec:
  template:
  spec:
    readinessProbe:
      # an http probe
      httpGet:
        path: /health/ready
        port: 80
      # length of time to wait for a pod to initialize
      # after pod startup, before applying health checking
      initialDelaySeconds: 30
      timeoutSeconds: 1
    ports:
      - containerPort: 80
```

## <a name="metric-based-probe-with-a-custom-response-writer"></a><span data-ttu-id="06785-292">사용자 지정 응답 기록기가 있는 메트릭 기반 프로브</span><span class="sxs-lookup"><span data-stu-id="06785-292">Metric-based probe with a custom response writer</span></span>

<span data-ttu-id="06785-293">샘플 앱은 사용자 지정 응답 기록기가 있는 메모리 상태를 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="06785-293">The sample app demonstrates a memory health check with a custom response writer.</span></span>

<span data-ttu-id="06785-294">앱이 일정한 메모리 임계값(샘플 앱의 경우 1GB)을 초과하여 사용하는 경우 `MemoryHealthCheck`는 성능이 저하된 상태를 보고합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-294">`MemoryHealthCheck` reports a degraded status if the app uses more than a given threshold of memory (1 GB in the sample app).</span></span> <span data-ttu-id="06785-295"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult>에는 앱의 가비지 수집기(GC) 정보가 포함됩니다(*MemoryHealthCheck.cs*).</span><span class="sxs-lookup"><span data-stu-id="06785-295">The <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> includes Garbage Collector (GC) information for the app (*MemoryHealthCheck.cs*):</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/MemoryHealthCheck.cs?name=snippet1)]

<span data-ttu-id="06785-296">`Startup.ConfigureServices`의 <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*>에 상태 검사 서비스를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-296">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="06785-297"><xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>에 전달하여 상태 검사를 사용하는 대신 `MemoryHealthCheck`는 서비스로 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="06785-297">Instead of enabling the health check by passing it to <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>, the `MemoryHealthCheck` is registered as a service.</span></span> <span data-ttu-id="06785-298">모든 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> 등록된 서비스는 상태 검사 서비스 및 미들웨어에 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="06785-298">All <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> registered services are available to the health check services and middleware.</span></span> <span data-ttu-id="06785-299">상태 확인 서비스를 Singleton 서비스로 등록하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="06785-299">We recommend registering health check services as Singleton services.</span></span>

<span data-ttu-id="06785-300">샘플 앱(*CustomWriterStartup.cs*)에서:</span><span class="sxs-lookup"><span data-stu-id="06785-300">In the sample app (*CustomWriterStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_ConfigureServices&highlight=4)]

<span data-ttu-id="06785-301">상태 검사 엔드포인트는 `Startup.Configure`에서 `MapHealthChecks`를 호출하여 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="06785-301">A health check endpoint is created by calling `MapHealthChecks` in `Startup.Configure`.</span></span> <span data-ttu-id="06785-302">상태 검사가 실행될 때 사용자 정의 JSON 응답을 출력하기 위해 `ResponseWriter` 속성에 `WriteResponse` 대리자가 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="06785-302">A `WriteResponse` delegate is provided to the `ResponseWriter` property to output a custom JSON response when the health check executes:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health", new HealthCheckOptions()
    {
        ResponseWriter = WriteResponse
    });
}
```

<span data-ttu-id="06785-303">`WriteResponse` 메서드는 `CompositeHealthCheckResult`를 JSON 객체로 형식 지정하고 상태 검사 응답을 위해 JSON 출력을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-303">The `WriteResponse` method formats the `CompositeHealthCheckResult` into a JSON object and yields JSON output for the health check response:</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_WriteResponse)]

<span data-ttu-id="06785-304">샘플 앱을 사용하여 사용자 지정 응답 기록기 출력으로 메트릭 기반 프로브를 실행하려면 명령 셸의 프로젝트 폴더에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-304">To run the metric-based probe with custom response writer output using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario writer
```

> [!NOTE]
> <span data-ttu-id="06785-305">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks)에는 디스크 스토리지 및 최댓값 활동성 검사를 포함하여 메트릭 기반 상태 확인 시나리오가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="06785-305">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) includes metric-based health check scenarios, including disk storage and maximum value liveness checks.</span></span>
>
> <span data-ttu-id="06785-306">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks)는 Microsoft에서 유지 관리하거나 지원하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="06785-306">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

## <a name="filter-by-port"></a><span data-ttu-id="06785-307">포트별 필터링</span><span class="sxs-lookup"><span data-stu-id="06785-307">Filter by port</span></span>

<span data-ttu-id="06785-308">지정된 포트에 대한 상태 검사 요청을 제한하는 포트를 지정하는 URL 패턴을 사용하여 `MapHealthChecks`에 `RequireHost`를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-308">Call `RequireHost` on `MapHealthChecks` with a URL pattern that specifies a port to restrict health check requests to the port specified.</span></span> <span data-ttu-id="06785-309">이는 일반적으로 컨테이너 환경에서 서비스 모니터링을 위해 포트를 노출하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="06785-309">This is typically used in a container environment to expose a port for monitoring services.</span></span>

<span data-ttu-id="06785-310">샘플 앱은 [환경 변수 구성 공급자](xref:fundamentals/configuration/index#environment-variables-configuration-provider)를 사용하여 포트를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-310">The sample app configures the port using the [Environment Variable Configuration Provider](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span></span> <span data-ttu-id="06785-311">포트는 *launchSettings.json* 파일에서 설정되고 환경 변수를 통해 구성 공급자에게 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="06785-311">The port is set in the *launchSettings.json* file and passed to the configuration provider via an environment variable.</span></span> <span data-ttu-id="06785-312">또한 관리 포트에서 요청을 수신하도록 서버를 구성해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-312">You must also configure the server to listen to requests on the management port.</span></span>

<span data-ttu-id="06785-313">샘플 앱을 사용하여 관리 포트 구성을 보여주려면 *launchSettings.json* 파일을 *Properties* 폴더에 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="06785-313">To use the sample app to demonstrate management port configuration, create the *launchSettings.json* file in a *Properties* folder.</span></span>

<span data-ttu-id="06785-314">샘플 앱의 다음 *Properties/launchSettings.json* 파일은 샘플 앱의 프로젝트 파일에 포함되지 않으며 수동으로 만들어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-314">The following *Properties/launchSettings.json* file in the sample app isn't included in the sample app's project files and must be created manually:</span></span>

```json
{
  "profiles": {
    "SampleApp": {
      "commandName": "Project",
      "commandLineArgs": "",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development",
        "ASPNETCORE_URLS": "http://localhost:5000/;http://localhost:5001/",
        "ASPNETCORE_MANAGEMENTPORT": "5001"
      },
      "applicationUrl": "http://localhost:5000/"
    }
  }
}
```

<span data-ttu-id="06785-315">`Startup.ConfigureServices`의 <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*>에 상태 검사 서비스를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-315">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="06785-316">`Startup.Configure`에서 `MapHealthChecks`를 호출하여 상태 검사 엔드포인트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="06785-316">Create a health check endpoint by calling `MapHealthChecks` in `Startup.Configure`.</span></span>

<span data-ttu-id="06785-317">샘플 앱에서 `Startup.Configure`의 엔드포인트에 대한 `RequireHost` 호출은 구성의 관리 포트를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-317">In the sample app, a call to `RequireHost` on the endpoint in `Startup.Configure` specifies the management port from configuration:</span></span>

```csharp
endpoints.MapHealthChecks("/health")
    .RequireHost($"*:{Configuration["ManagementPort"]}");
```

<span data-ttu-id="06785-318">샘플 앱에서 엔드포인트는 `Startup.Configure`에서 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="06785-318">Endpoints are created in the sample app in `Startup.Configure`.</span></span> <span data-ttu-id="06785-319">다음 예제 코드에서:</span><span class="sxs-lookup"><span data-stu-id="06785-319">In the following example code:</span></span>

* <span data-ttu-id="06785-320">준비 상태 검사는 'ready' 태그가 있는 등록된 검사를 모두 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-320">The readiness check uses all registered checks with the 'ready' tag.</span></span>
* <span data-ttu-id="06785-321">`Predicate`는 모든 검사를 제외하고 200-Ok를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-321">The `Predicate` excludes all checks and return a 200-Ok.</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health/ready", new HealthCheckOptions()
    {
        Predicate = (check) => check.Tags.Contains("ready"),
    });

    endpoints.MapHealthChecks("/health/live", new HealthCheckOptions()
    {
        Predicate = (_) => false
    });
}
```

> [!NOTE]
> <span data-ttu-id="06785-322">코드에서 관리 포트를 명시적으로 설정하여 샘플 앱에 *launchSettings.json* 파일을 만들지 않도록 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="06785-322">You can avoid creating the *launchSettings.json* file in the sample app by setting the management port explicitly in code.</span></span> <span data-ttu-id="06785-323"><xref:Microsoft.Extensions.Hosting.HostBuilder>가 만들어진 *Program.cs*에서 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenAnyIP*>에 대한 호출을 추가하고 앱의 관리 포트 엔드포인트를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-323">In *Program.cs* where the <xref:Microsoft.Extensions.Hosting.HostBuilder> is created, add a call to <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenAnyIP*> and provide the app's management port endpoint.</span></span> <span data-ttu-id="06785-324">*ManagementPortStartup.cs*의 `Configure`에서 `RequireHost`로 관리 포트를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-324">In `Configure` of *ManagementPortStartup.cs*, specify the management port with `RequireHost`:</span></span>
>
> <span data-ttu-id="06785-325">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="06785-325">*Program.cs*:</span></span>
>
> ```csharp
> return new HostBuilder()
>     .ConfigureWebHostDefaults(webBuilder =>
>     {
>         webBuilder.UseKestrel()
>             .ConfigureKestrel(serverOptions =>
>             {
>                 serverOptions.ListenAnyIP(5001);
>             })
>             .UseStartup(startupType);
>     })
>     .Build();
> ```
>
> <span data-ttu-id="06785-326">*ManagementPortStartup.cs*:</span><span class="sxs-lookup"><span data-stu-id="06785-326">*ManagementPortStartup.cs*:</span></span>
>
> ```csharp
> app.UseEndpoints(endpoints =>
> {
>     endpoints.MapHealthChecks("/health").RequireHost("*:5001");
> });
> ```

<span data-ttu-id="06785-327">샘플 앱을 사용하여 관리 포트 구성 시나리오를 실행하려면 명령 셸의 프로젝트 폴더에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-327">To run the management port configuration scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario port
```

## <a name="distribute-a-health-check-library"></a><span data-ttu-id="06785-328">상태 검사 라이브러리 배포</span><span class="sxs-lookup"><span data-stu-id="06785-328">Distribute a health check library</span></span>

<span data-ttu-id="06785-329">상태 검사를 라이브러리로 배포하려면 다음과 같이 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-329">To distribute a health check as a library:</span></span>

1. <span data-ttu-id="06785-330"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> 인터페이스를 독립 실행형 클래스로 구현하는 상태 검사를 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-330">Write a health check that implements the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> interface as a standalone class.</span></span> <span data-ttu-id="06785-331">클래스는 [DI(종속성 주입)](xref:fundamentals/dependency-injection), 유형 활성화 및 [명명된 옵션](xref:fundamentals/configuration/options)에 의존하여 구성 데이터에 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="06785-331">The class can rely on [dependency injection (DI)](xref:fundamentals/dependency-injection), type activation, and [named options](xref:fundamentals/configuration/options) to access configuration data.</span></span>

   <span data-ttu-id="06785-332">`CheckHealthAsync`의 상태 검사 논리에서:</span><span class="sxs-lookup"><span data-stu-id="06785-332">In the health checks logic of `CheckHealthAsync`:</span></span>

   * <span data-ttu-id="06785-333">`data1` 및 `data2`는 메서드에서 프로브의 상태 검사 논리를 실행하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="06785-333">`data1` and `data2` are used in the method to run the probe's health check logic.</span></span>
   * <span data-ttu-id="06785-334">`AccessViolationException`이 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="06785-334">`AccessViolationException` is handled.</span></span>

   <span data-ttu-id="06785-335"><xref:System.AccessViolationException>이 발생하는 경우 사용자가 상태 검사 실패 상태를 구성할 수 있도록 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.FailureStatus>가 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult>와 함께 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="06785-335">When an <xref:System.AccessViolationException> occurs, the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.FailureStatus> is returned with the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> to allow users to configure the health checks failure status.</span></span>

   ```csharp
   using System;
   using System.Threading;
   using System.Threading.Tasks;
   using Microsoft.Extensions.Diagnostics.HealthChecks;

   namespace SampleApp
   {
       public class ExampleHealthCheck : IHealthCheck
       {
           private readonly string _data1;
           private readonly int? _data2;

           public ExampleHealthCheck(string data1, int? data2)
           {
               _data1 = data1 ?? throw new ArgumentNullException(nameof(data1));
               _data2 = data2 ?? throw new ArgumentNullException(nameof(data2));
           }

           public async Task<HealthCheckResult> CheckHealthAsync(
               HealthCheckContext context, CancellationToken cancellationToken)
           {
               try
               {
                   return HealthCheckResult.Healthy();
               }
               catch (AccessViolationException ex)
               {
                   return new HealthCheckResult(
                       context.Registration.FailureStatus,
                       description: "An access violation occurred during the check.",
                       exception: ex,
                       data: null);
               }
           }
       }
   }
   ```

1. <span data-ttu-id="06785-336">사용하는 앱이 `Startup.Configure` 메서드에서 호출하는 매개 변수를 사용하여 확장 메서드를 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-336">Write an extension method with parameters that the consuming app calls in its `Startup.Configure` method.</span></span> <span data-ttu-id="06785-337">다음 예제에서는 다음 상태 검사 메서드 서명을 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-337">In the following example, assume the following health check method signature:</span></span>

   ```csharp
   ExampleHealthCheck(string, string, int )
   ```

   <span data-ttu-id="06785-338">이전 서명은 `ExampleHealthCheck`가 상태 검사 프로브 로직을 처리하기 위해 추가 데이터가 필요함을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="06785-338">The preceding signature indicates that the `ExampleHealthCheck` requires additional data to process the health check probe logic.</span></span> <span data-ttu-id="06785-339">상태 검사가 확장 메서드를 사용하여 등록되면 데이터는 상태 검사 인스턴스를 만드는 데 사용된 대리자에게 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="06785-339">The data is provided to the delegate used to create the health check instance when the health check is registered with an extension method.</span></span> <span data-ttu-id="06785-340">다음 예제에서 호출자는 선택 사항을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-340">In the following example, the caller specifies optional:</span></span>

   * <span data-ttu-id="06785-341">상태 검사 이름(`name`).</span><span class="sxs-lookup"><span data-stu-id="06785-341">health check name (`name`).</span></span> <span data-ttu-id="06785-342">`null`인 경우 `example_health_check`가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="06785-342">If `null`, `example_health_check` is used.</span></span>
   * <span data-ttu-id="06785-343">상태 검사를 위한 문자열 데이터 요소입니다(`data1`).</span><span class="sxs-lookup"><span data-stu-id="06785-343">string data point for the health check (`data1`).</span></span>
   * <span data-ttu-id="06785-344">상태 검사를 위한 정수 데이터 요소입니다(`data2`).</span><span class="sxs-lookup"><span data-stu-id="06785-344">integer data point for the health check (`data2`).</span></span> <span data-ttu-id="06785-345">`null`인 경우 `1`이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="06785-345">If `null`, `1` is used.</span></span>
   * <span data-ttu-id="06785-346">실패 상태입니다(<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>).</span><span class="sxs-lookup"><span data-stu-id="06785-346">failure status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>).</span></span> <span data-ttu-id="06785-347">기본값은 `null`입니다.</span><span class="sxs-lookup"><span data-stu-id="06785-347">The default is `null`.</span></span> <span data-ttu-id="06785-348">`null`인 경우 오류 상태는 [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus)로 보고됩니다.</span><span class="sxs-lookup"><span data-stu-id="06785-348">If `null`, [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) is reported for a failure status.</span></span>
   * <span data-ttu-id="06785-349">태그(`IEnumerable<string>`)입니다.</span><span class="sxs-lookup"><span data-stu-id="06785-349">tags (`IEnumerable<string>`).</span></span>

   ```csharp
   using System.Collections.Generic;
   using Microsoft.Extensions.Diagnostics.HealthChecks;

   public static class ExampleHealthCheckBuilderExtensions
   {
       const string DefaultName = "example_health_check";

       public static IHealthChecksBuilder AddExampleHealthCheck(
           this IHealthChecksBuilder builder,
           string name = default,
           string data1,
           int data2 = 1,
           HealthStatus? failureStatus = default,
           IEnumerable<string> tags = default)
       {
           return builder.Add(new HealthCheckRegistration(
               name ?? DefaultName,
               sp => new ExampleHealthCheck(data1, data2),
               failureStatus,
               tags));
       }
   }
   ```

## <a name="health-check-publisher"></a><span data-ttu-id="06785-350">상태 검사 게시자</span><span class="sxs-lookup"><span data-stu-id="06785-350">Health Check Publisher</span></span>

<span data-ttu-id="06785-351"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher>가 서비스 컨테이너에 추가되면 상태 검사 시스템이 주기적으로 상태 검사를 실행하고 그 결과로 `PublishAsync`를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-351">When an <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> is added to the service container, the health check system periodically executes your health checks and calls `PublishAsync` with the result.</span></span> <span data-ttu-id="06785-352">이는 각 프로세스가 상태를 결정하기 위해 주기적으로 모니터링 시스템을 호출하도록 하는 푸시 기반 상태 모니터링 시스템 시나리오에서 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-352">This is useful in a push-based health monitoring system scenario that expects each process to call the monitoring system periodically in order to determine health.</span></span>

<span data-ttu-id="06785-353"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> 인터페이스는 단일 메서드를 가집니다.</span><span class="sxs-lookup"><span data-stu-id="06785-353">The <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> interface has a single method:</span></span>

```csharp
Task PublishAsync(HealthReport report, CancellationToken cancellationToken);
```

<span data-ttu-id="06785-354"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions>를 사용하여 다음을 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="06785-354"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions> allow you to set:</span></span>

* <span data-ttu-id="06785-355"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay> &ndash; 앱이 시작된 후 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> 인스턴스를 시작하기 전에 적용되는 초기 지연입니다.</span><span class="sxs-lookup"><span data-stu-id="06785-355"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay> &ndash; The initial delay applied after the app starts before executing <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instances.</span></span> <span data-ttu-id="06785-356">지연은 시작 시 한 번 적용되며 후속 반복에는 적용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="06785-356">The delay is applied once at startup and doesn't apply to subsequent iterations.</span></span> <span data-ttu-id="06785-357">기본값은 5초입니다.</span><span class="sxs-lookup"><span data-stu-id="06785-357">The default value is five seconds.</span></span>
* <span data-ttu-id="06785-358"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period> &ndash; <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> 실행 기간입니다.</span><span class="sxs-lookup"><span data-stu-id="06785-358"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period> &ndash; The period of <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> execution.</span></span> <span data-ttu-id="06785-359">기본값은 30초입니다.</span><span class="sxs-lookup"><span data-stu-id="06785-359">The default value is 30 seconds.</span></span>
* <span data-ttu-id="06785-360"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> &ndash; <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate>가 `null`(기본값)인 경우 상태 검사 게시자 서비스는 등록된 상태 검사를 모두 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-360"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> &ndash; If <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> is `null` (default), the health check publisher service runs all registered health checks.</span></span> <span data-ttu-id="06785-361">상태 검사 하위 집합을 실행하려면 검사 세트를 필터링하는 함수를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-361">To run a subset of health checks, provide a function that filters the set of checks.</span></span> <span data-ttu-id="06785-362">조건자는 기간마다 평가됩니다.</span><span class="sxs-lookup"><span data-stu-id="06785-362">The predicate is evaluated each period.</span></span>
* <span data-ttu-id="06785-363"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Timeout> &ndash; 모든 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> 인스턴스에 대한 상태 검사 실행의 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="06785-363"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Timeout> &ndash; The timeout for executing the health checks for all <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instances.</span></span> <span data-ttu-id="06785-364">시간 제한 없이 실행하려면 <xref:System.Threading.Timeout.InfiniteTimeSpan>을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-364">Use <xref:System.Threading.Timeout.InfiniteTimeSpan> to execute without a timeout.</span></span> <span data-ttu-id="06785-365">기본값은 30초입니다.</span><span class="sxs-lookup"><span data-stu-id="06785-365">The default value is 30 seconds.</span></span>

<span data-ttu-id="06785-366">샘플 앱에서 `ReadinessPublisher`는 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> 구현입니다.</span><span class="sxs-lookup"><span data-stu-id="06785-366">In the sample app, `ReadinessPublisher` is an <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation.</span></span> <span data-ttu-id="06785-367">상태 검사 상태는 다음의 로그 수준에서 각 검사에 대해 기록됩니다.</span><span class="sxs-lookup"><span data-stu-id="06785-367">The health check status is logged for each check at a log level of:</span></span>

* <span data-ttu-id="06785-368">상태 검사 상태가 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Healthy>인 경우 정보(<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*>)</span><span class="sxs-lookup"><span data-stu-id="06785-368">Information (<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*>) if the health checks status is <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Healthy>.</span></span>
* <span data-ttu-id="06785-369">상태가 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Degraded> 또는 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Unhealthy>인 경우 오류(<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError*>)</span><span class="sxs-lookup"><span data-stu-id="06785-369">Error (<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError*>) if the status is either <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Degraded> or <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Unhealthy>.</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/ReadinessPublisher.cs?name=snippet_ReadinessPublisher&highlight=18-27)]

<span data-ttu-id="06785-370">샘플 앱의 `LivenessProbeStartup` 예제에서 `StartupHostedService` 준비 검사에는 2초 시작 지연이 있고 30초마다 검사가 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="06785-370">In the sample app's `LivenessProbeStartup` example, the `StartupHostedService` readiness check has a two second startup delay and runs the check every 30 seconds.</span></span> <span data-ttu-id="06785-371"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> 구현을 활성화하기 위해 샘플은 [DI(종속성 주입)](xref:fundamentals/dependency-injection) 컨테이너에서 `ReadinessPublisher`를 싱글톤 서비스로 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-371">To activate the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation, the sample registers `ReadinessPublisher` as a singleton service in the [dependency injection (DI)](xref:fundamentals/dependency-injection) container:</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_ConfigureServices)]

> [!NOTE]
> <span data-ttu-id="06785-372">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks)는 [Application Insights](/azure/application-insights/app-insights-overview)를 포함하여 몇몇 시스템에 대한 게시자를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-372">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) includes publishers for several systems, including [Application Insights](/azure/application-insights/app-insights-overview).</span></span>
>
> <span data-ttu-id="06785-373">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks)는 Microsoft에서 유지 관리하거나 지원하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="06785-373">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

## <a name="restrict-health-checks-with-mapwhen"></a><span data-ttu-id="06785-374">MapWhen으로 상태 검사 제한</span><span class="sxs-lookup"><span data-stu-id="06785-374">Restrict health checks with MapWhen</span></span>

<span data-ttu-id="06785-375"><xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*>을 사용하여 상태 검사 엔드포인트에 대한 요청 파이프라인을 조건부로 분기합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-375">Use <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> to conditionally branch the request pipeline for health check endpoints.</span></span>

<span data-ttu-id="06785-376">다음 예제에서 `MapWhen`은 `api/HealthCheck` 엔드포인트에 대한 GET 요청을 받는 경우 상태 검사 미들웨어를 활성화하도록 요청 파이프라인을 분기합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-376">In the following example, `MapWhen` branches the request pipeline to activate Health Checks Middleware if a GET request is received for the `api/HealthCheck` endpoint:</span></span>

```csharp
app.MapWhen(
    context => context.Request.Method == HttpMethod.Get.Method && 
        context.Request.Path.StartsWith("/api/HealthCheck"),
    builder => builder.UseHealthChecks());

app.UseEndpoints(endpoints =>
{
    endpoints.MapRazorPages();
});
```

<span data-ttu-id="06785-377">자세한 내용은 <xref:fundamentals/middleware/index#use-run-and-map>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="06785-377">For more information, see <xref:fundamentals/middleware/index#use-run-and-map>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="06785-378">ASP.NET Core는 앱 인프라 구성 요소의 상태를 보고하기 위해 상태 검사 미들웨어와 라이브러리를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-378">ASP.NET Core offers Health Checks Middleware and libraries for reporting the health of app infrastructure components.</span></span>

<span data-ttu-id="06785-379">상태 검사는 앱에 의해 HTTP 엔드포인트로서 노출됩니다.</span><span class="sxs-lookup"><span data-stu-id="06785-379">Health checks are exposed by an app as HTTP endpoints.</span></span> <span data-ttu-id="06785-380">상태 검사 엔드포인트는 다양한 실시간 모니터링 시나리오에 맞게 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="06785-380">Health check endpoints can be configured for a variety of real-time monitoring scenarios:</span></span>

* <span data-ttu-id="06785-381">상태 프로브는 컨테이너 오케스트레이터와 부하 분산 장치가 앱 상태를 검사하는 데 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="06785-381">Health probes can be used by container orchestrators and load balancers to check an app's status.</span></span> <span data-ttu-id="06785-382">예를 들어, 컨테이너 오케스트레이터는 롤링 배포를 중지하거나 컨테이너를 다시 시작하여 실패한 상태 검사에 응답할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="06785-382">For example, a container orchestrator may respond to a failing health check by halting a rolling deployment or restarting a container.</span></span> <span data-ttu-id="06785-383">부하 분산 장치는 장애가 발생한 상태 검사 인스턴스로부터 트래픽을 다른 곳으로 라우팅하여 비정상 앱에 대응할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="06785-383">A load balancer might react to an unhealthy app by routing traffic away from the failing instance to a healthy instance.</span></span>
* <span data-ttu-id="06785-384">메모리, 디스크 및 기타 물리적 서버 리소스의 사용이 정상적인 상태인지 모니터링할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="06785-384">Use of memory, disk, and other physical server resources can be monitored for healthy status.</span></span>
* <span data-ttu-id="06785-385">상태 검사는 데이터베이스 및 외부 서비스 엔드포인트와 같은 앱 종속성을 테스트하여 가용성 및 정상 작동 여부를 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="06785-385">Health checks can test an app's dependencies, such as databases and external service endpoints, to confirm availability and normal functioning.</span></span>

<span data-ttu-id="06785-386">[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/health-checks/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="06785-386">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/health-checks/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="06785-387">샘플 앱에는 이 항목에 설명된 시나리오의 예가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="06785-387">The sample app includes examples of the scenarios described in this topic.</span></span> <span data-ttu-id="06785-388">지정된 시나리오에 대해 샘플 앱을 실행하려면 명령 셸의 프로젝트 폴더에서 [dotnet run](/dotnet/core/tools/dotnet-run) 명령을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-388">To run the sample app for a given scenario, use the [dotnet run](/dotnet/core/tools/dotnet-run) command from the project's folder in a command shell.</span></span> <span data-ttu-id="06785-389">샘플 앱의 사용 방법에 대한 자세한 내용은 샘플 앱의 *README.md* 파일과 이 항목의 시나리오 설명을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="06785-389">See the sample app's *README.md* file and the scenario descriptions in this topic for details on how to use the sample app.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="06785-390">전제 조건</span><span class="sxs-lookup"><span data-stu-id="06785-390">Prerequisites</span></span>

<span data-ttu-id="06785-391">상태 검사는 일반적으로 외부 모니터링 서비스 또는 컨테이너 오케스트레이터와 함께 사용되어 앱 상태를 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-391">Health checks are usually used with an external monitoring service or container orchestrator to check the status of an app.</span></span> <span data-ttu-id="06785-392">앱에 상태 검사를 추가하기 전에 사용할 모니터링 시스템을 결정합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-392">Before adding health checks to an app, decide on which monitoring system to use.</span></span> <span data-ttu-id="06785-393">모니터링 시스템은 어떤 유형의 상태 검사를 만들고 그 엔드포인트를 구성하는 방법을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-393">The monitoring system dictates what types of health checks to create and how to configure their endpoints.</span></span>

<span data-ttu-id="06785-394">[Microsoft.AspNetCore.App 메타패키지](xref:fundamentals/metapackage-app)를 참조하거나 [Microsoft.AspNetCore.Diagnostics.HealthChecks](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.HealthChecks) 패키지에 대한 패키지 참조를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-394">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.AspNetCore.Diagnostics.HealthChecks](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.HealthChecks) package.</span></span>

<span data-ttu-id="06785-395">샘플 앱은 여러 시나리오의 상태 검사를 보여주는 시작 코드를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-395">The sample app provides startup code to demonstrate health checks for several scenarios.</span></span> <span data-ttu-id="06785-396">[데이터베이스 프로브](#database-probe) 시나리오는 [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks)를 사용하여 데이터베이스 연결의 상태를 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-396">The [database probe](#database-probe) scenario checks the health of a database connection using [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks).</span></span> <span data-ttu-id="06785-397">[DbContext 프로브](#entity-framework-core-dbcontext-probe) 시나리오는 EF Core `DbContext`를 사용하여 데이터베이스를 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-397">The [DbContext probe](#entity-framework-core-dbcontext-probe) scenario checks a database using an EF Core `DbContext`.</span></span> <span data-ttu-id="06785-398">데이터베이스 시나리오를 탐색하려면 샘플 앱:</span><span class="sxs-lookup"><span data-stu-id="06785-398">To explore the database scenarios, the sample app:</span></span>

* <span data-ttu-id="06785-399">데이터베이스를 만들고 *appsettings.json* 파일에서 연결 문자열을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-399">Creates a database and provides its connection string in the *appsettings.json* file.</span></span>
* <span data-ttu-id="06785-400">해당 프로젝트 파일에 다음 패키지 참조가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="06785-400">Has the following package references in its project file:</span></span>
  * [<span data-ttu-id="06785-401">AspNetCore.HealthChecks.SqlServer</span><span class="sxs-lookup"><span data-stu-id="06785-401">AspNetCore.HealthChecks.SqlServer</span></span>](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/)
  * [<span data-ttu-id="06785-402">Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="06785-402">Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/)

> [!NOTE]
> <span data-ttu-id="06785-403">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks)는 Microsoft에서 유지 관리하거나 지원하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="06785-403">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

<span data-ttu-id="06785-404">다른 상태 검사 시나리오는 관리 포트에 대해 상태 검사를 필터링하는 방법을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="06785-404">Another health check scenario demonstrates how to filter health checks to a management port.</span></span> <span data-ttu-id="06785-405">샘플 앱을 사용하려면 관리 URL과 관리 포트가 포함된 *Properties/launchSettings.json*  파일을 만들어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-405">The sample app requires you to create a *Properties/launchSettings.json* file that includes the management URL and management port.</span></span> <span data-ttu-id="06785-406">자세한 내용은 [포트별 필터링](#filter-by-port) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="06785-406">For more information, see the [Filter by port](#filter-by-port) section.</span></span>

## <a name="basic-health-probe"></a><span data-ttu-id="06785-407">기본 상태 프로브</span><span class="sxs-lookup"><span data-stu-id="06785-407">Basic health probe</span></span>

<span data-ttu-id="06785-408">많은 앱의 경우, 요청을 처리할 수 있는 앱의 가용성(*활동성*)을 보고하는 기본 상태 검사 구성으로 앱 상태를 충분히 파악할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="06785-408">For many apps, a basic health probe configuration that reports the app's availability to process requests (*liveness*) is sufficient to discover the status of the app.</span></span>

<span data-ttu-id="06785-409">기본 구성은 상태 검사 서비스를 등록하고 상태 검사 미들웨어를 호출하여 URL 엔드포인트에서 상태 응답을 사용하여 응답합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-409">The basic configuration registers health check services and calls the Health Checks Middleware to respond at a URL endpoint with a health response.</span></span> <span data-ttu-id="06785-410">기본적으로 특정 종속성이나 하위 시스템을 테스트하기 위해 특정 상태 검사가 등록되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="06785-410">By default, no specific health checks are registered to test any particular dependency or subsystem.</span></span> <span data-ttu-id="06785-411">상태 엔드포인트 URL에서 응답할 수 있는 경우 앱 상태가 좋은 것으로 간주됩니다.</span><span class="sxs-lookup"><span data-stu-id="06785-411">The app is considered healthy if it's capable of responding at the health endpoint URL.</span></span> <span data-ttu-id="06785-412">기본 응답 기록기는 상태(<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>)를 일반 텍스트 응답으로 다시 클라이언트에 기록하여 [HealthStatus.Healthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus), [HealthStatus.Degraded](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) 또는 [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) 상태를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="06785-412">The default response writer writes the status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) as a plaintext response back to the client, indicating either a [HealthStatus.Healthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus), [HealthStatus.Degraded](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) or [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) status.</span></span>

<span data-ttu-id="06785-413">`Startup.ConfigureServices`의 <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*>에 상태 검사 서비스를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-413">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="06785-414">`Startup.Configure`의 요청 처리 파이프라인에 있는 <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*>로 상태 검사 미들웨어용 엔드포인트를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-414">Add an endpoint for Health Checks Middleware with <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> in the request processing pipeline of `Startup.Configure`.</span></span>

<span data-ttu-id="06785-415">샘플 앱에서 상태 검사 엔드포인트는 `/health`(*BasicStartup.cs*)에 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="06785-415">In the sample app, the health check endpoint is created at `/health` (*BasicStartup.cs*):</span></span>

```csharp
public class BasicStartup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddHealthChecks();
    }

    public void Configure(IApplicationBuilder app)
    {
        app.UseHealthChecks("/health");
    }
}
```

<span data-ttu-id="06785-416">샘플 앱을 사용하여 기본 구성 시나리오를 실행하려면 명령 셸의 프로젝트 폴더에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-416">To run the basic configuration scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario basic
```

### <a name="docker-example"></a><span data-ttu-id="06785-417">Docker 예</span><span class="sxs-lookup"><span data-stu-id="06785-417">Docker example</span></span>

<span data-ttu-id="06785-418">[Docker](xref:host-and-deploy/docker/index)는 기본 상태 검사 구성을 사용하는 앱의 상태를 검사하는 데 사용할 수 있는 기본 제공 `HEALTHCHECK` 지시문을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-418">[Docker](xref:host-and-deploy/docker/index) offers a built-in `HEALTHCHECK` directive that can be used to check the status of an app that uses the basic health check configuration:</span></span>

```
HEALTHCHECK CMD curl --fail http://localhost:5000/health || exit
```

## <a name="create-health-checks"></a><span data-ttu-id="06785-419">상태 검사 만들기</span><span class="sxs-lookup"><span data-stu-id="06785-419">Create health checks</span></span>

<span data-ttu-id="06785-420">상태 검사는 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> 인터페이스를 구현하여 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="06785-420">Health checks are created by implementing the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> interface.</span></span> <span data-ttu-id="06785-421"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*> 메서드는 상태를 `Healthy`, `Degraded` 또는 `Unhealthy`로 나타내는 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult>를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-421">The <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*> method returns a <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> that indicates the health as `Healthy`, `Degraded`, or `Unhealthy`.</span></span> <span data-ttu-id="06785-422">결과는 구성 가능한 상태 코드가 있는 일반 텍스트 응답으로 기록됩니다(구성은 [상태 검사 옵션](#health-check-options) 섹션에 설명되어 있음).</span><span class="sxs-lookup"><span data-stu-id="06785-422">The result is written as a plaintext response with a configurable status code (configuration is described in the [Health check options](#health-check-options) section).</span></span> <span data-ttu-id="06785-423"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult>는 선택 사항인 키-값 쌍을 반환할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="06785-423"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> can also return optional key-value pairs.</span></span>

### <a name="example-health-check"></a><span data-ttu-id="06785-424">상태 검사 예</span><span class="sxs-lookup"><span data-stu-id="06785-424">Example health check</span></span>

<span data-ttu-id="06785-425">다음 `ExampleHealthCheck` 클래스는 상태 검사의 레이아웃을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="06785-425">The following `ExampleHealthCheck` class demonstrates the layout of a health check.</span></span> <span data-ttu-id="06785-426">상태 검사 논리는 `CheckHealthAsync` 메서드에 배치됩니다.</span><span class="sxs-lookup"><span data-stu-id="06785-426">The health checks logic is placed in the `CheckHealthAsync` method.</span></span> <span data-ttu-id="06785-427">다음 예제에서는 더미 변수 `healthCheckResultHealthy`를 `true`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-427">The following example sets a dummy variable, `healthCheckResultHealthy`, to `true`.</span></span> <span data-ttu-id="06785-428">`healthCheckResultHealthy` 값이 `false`로 설정되면 [HealthCheckResult.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult.Unhealthy*) 상태가 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="06785-428">If the value of `healthCheckResultHealthy` is set to `false`, the [HealthCheckResult.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult.Unhealthy*) status is returned.</span></span>

```csharp
public class ExampleHealthCheck : IHealthCheck
{
    public Task<HealthCheckResult> CheckHealthAsync(
        HealthCheckContext context,
        CancellationToken cancellationToken = default(CancellationToken))
    {
        var healthCheckResultHealthy = true;

        if (healthCheckResultHealthy)
        {
            return Task.FromResult(
                HealthCheckResult.Healthy("The check indicates a healthy result."));
        }

        return Task.FromResult(
            HealthCheckResult.Unhealthy("The check indicates an unhealthy result."));
    }
}
```

### <a name="register-health-check-services"></a><span data-ttu-id="06785-429">상태 검사 서비스 등록</span><span class="sxs-lookup"><span data-stu-id="06785-429">Register health check services</span></span>

<span data-ttu-id="06785-430">`ExampleHealthCheck` 형식은 <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>로 `Startup.ConfigureServices`의 상태 검사 서비스에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="06785-430">The `ExampleHealthCheck` type is added to health check services in `Startup.ConfigureServices` with <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>:</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck<ExampleHealthCheck>("example_health_check");
```

<span data-ttu-id="06785-431">다음 예제에 표시된 <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> 오버로드는 상태 검사에서 오류가 보고되면 보고하도록 오류 상태(<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>)를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-431">The <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> overload shown in the following example sets the failure status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) to report when the health check reports a failure.</span></span> <span data-ttu-id="06785-432">오류 상태가 `null`(기본값)로 설정되면 [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus)가 보고됩니다.</span><span class="sxs-lookup"><span data-stu-id="06785-432">If the failure status is set to `null` (default), [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) is reported.</span></span> <span data-ttu-id="06785-433">이 오버로드는 라이브러리 작성자에게 유용한 시나리오입니다. 이 라이브러리에서는 상태 검사 구현이 설정을 준수하는 경우 상태 검사 실패가 발생할 때 라이브러리가 나타내는 오류 상태는 앱에 의해 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="06785-433">This overload is a useful scenario for library authors, where the failure status indicated by the library is enforced by the app when a health check failure occurs if the health check implementation honors the setting.</span></span>

<span data-ttu-id="06785-434">*태그*를 사용하여 상태 검사를 필터링할 수 있습니다([필터 상태 검사](#filter-health-checks) 섹션에 자세히 설명되어 있음).</span><span class="sxs-lookup"><span data-stu-id="06785-434">*Tags* can be used to filter health checks (described further in the [Filter health checks](#filter-health-checks) section).</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck<ExampleHealthCheck>(
        "example_health_check",
        failureStatus: HealthStatus.Degraded,
        tags: new[] { "example" });
```

<span data-ttu-id="06785-435"><xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>는 람다 함수를 실행할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="06785-435"><xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> can also execute a lambda function.</span></span> <span data-ttu-id="06785-436">다음 `Startup.ConfigureServices` 예제에서 상태 검사 이름은 `Example`로 지정되고 검사는 항상 정상 상태를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-436">In the following `Startup.ConfigureServices` example, the health check name is specified as `Example` and the check always returns a healthy state:</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck("Example", () =>
        HealthCheckResult.Healthy("Example is OK!"), tags: new[] { "example" });
```

### <a name="use-health-checks-middleware"></a><span data-ttu-id="06785-437">상태 검사 미들웨어 사용</span><span class="sxs-lookup"><span data-stu-id="06785-437">Use Health Checks Middleware</span></span>

<span data-ttu-id="06785-438">`Startup.Configure`에서 엔드포인트 URL 또는 상대 경로를 사용하여 처리 파이프라인에서 <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*>를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-438">In `Startup.Configure`, call <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> in the processing pipeline with the endpoint URL or relative path:</span></span>

```csharp
app.UseHealthChecks("/health");
```

<span data-ttu-id="06785-439">상태 검사가 특정 포트에서 수신 대기해야 하는 경우 <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*>의 오버로드를 사용하여 포트를 설정합니다([포트별 필터링 ](#filter-by-port) 섹션에 자세히 설명되어 있음).</span><span class="sxs-lookup"><span data-stu-id="06785-439">If the health checks should listen on a specific port, use an overload of <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> to set the port (described further in the [Filter by port](#filter-by-port) section):</span></span>

```csharp
app.UseHealthChecks("/health", port: 8000);
```

## <a name="health-check-options"></a><span data-ttu-id="06785-440">상태 검사 옵션</span><span class="sxs-lookup"><span data-stu-id="06785-440">Health check options</span></span>

<span data-ttu-id="06785-441"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions>는 다음과 같은 상태 검사 동작을 사용자 지정할 수 있는 기회를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-441"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions> provide an opportunity to customize health check behavior:</span></span>

* [<span data-ttu-id="06785-442">상태 검사 필터링</span><span class="sxs-lookup"><span data-stu-id="06785-442">Filter health checks</span></span>](#filter-health-checks)
* [<span data-ttu-id="06785-443">HTTP 상태 코드 사용자 지정</span><span class="sxs-lookup"><span data-stu-id="06785-443">Customize the HTTP status code</span></span>](#customize-the-http-status-code)
* [<span data-ttu-id="06785-444">캐시 헤더 표시 안 함</span><span class="sxs-lookup"><span data-stu-id="06785-444">Suppress cache headers</span></span>](#suppress-cache-headers)
* [<span data-ttu-id="06785-445">출력 사용자 지정</span><span class="sxs-lookup"><span data-stu-id="06785-445">Customize output</span></span>](#customize-output)

### <a name="filter-health-checks"></a><span data-ttu-id="06785-446">상태 검사 필터링</span><span class="sxs-lookup"><span data-stu-id="06785-446">Filter health checks</span></span>

<span data-ttu-id="06785-447">기본적으로 상태 검사 미들웨어는 등록된 모든 상태 검사를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-447">By default, Health Checks Middleware runs all registered health checks.</span></span> <span data-ttu-id="06785-448">상태 검사 하위 세트를 실행하려면 <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate> 옵션에 부울 값을 반환하는 함수를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-448">To run a subset of health checks, provide a function that returns a boolean to the <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate> option.</span></span> <span data-ttu-id="06785-449">다음 예제에서는 상태 검사의 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.Tags> 속성이 `foo_tag` 또는 `baz_tag`와 일치하는 경우에만 `true`가 반환되는 함수의 조건문에서 태그(`bar_tag`)에 의해 `Bar` 상태 검사가 필터링됩니다.</span><span class="sxs-lookup"><span data-stu-id="06785-449">In the following example, the `Bar` health check is filtered out by its tag (`bar_tag`) in the function's conditional statement, where `true` is only returned if the health check's <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.Tags> property matches `foo_tag` or `baz_tag`:</span></span>

```csharp
using System.Threading.Tasks;
using Microsoft.AspNetCore.Diagnostics.HealthChecks;
using Microsoft.Extensions.Diagnostics.HealthChecks;

public void ConfigureServices(IServiceCollection services)
{
    services.AddHealthChecks()
        .AddCheck("Foo", () =>
            HealthCheckResult.Healthy("Foo is OK!"), tags: new[] { "foo_tag" })
        .AddCheck("Bar", () =>
            HealthCheckResult.Unhealthy("Bar is unhealthy!"), 
                tags: new[] { "bar_tag" })
        .AddCheck("Baz", () =>
            HealthCheckResult.Healthy("Baz is OK!"), tags: new[] { "baz_tag" });
}

public void Configure(IApplicationBuilder app)
{
    app.UseHealthChecks("/health", new HealthCheckOptions()
    {
        Predicate = (check) => check.Tags.Contains("foo_tag") ||
            check.Tags.Contains("baz_tag")
    });
}
```

### <a name="customize-the-http-status-code"></a><span data-ttu-id="06785-450">HTTP 상태 코드 사용자 지정</span><span class="sxs-lookup"><span data-stu-id="06785-450">Customize the HTTP status code</span></span>

<span data-ttu-id="06785-451"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResultStatusCodes>를 사용하여 상태를 HTTP 상태 코드에 매핑하는 작업을 사용자 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="06785-451">Use <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResultStatusCodes> to customize the mapping of health status to HTTP status codes.</span></span> <span data-ttu-id="06785-452">다음 <xref:Microsoft.AspNetCore.Http.StatusCodes> 할당은 미들웨어에서 사용되는 기본값입니다.</span><span class="sxs-lookup"><span data-stu-id="06785-452">The following <xref:Microsoft.AspNetCore.Http.StatusCodes> assignments are the default values used by the middleware.</span></span> <span data-ttu-id="06785-453">요구 사항에 맞게 상태 코드 값을 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-453">Change the status code values to meet your requirements.</span></span>

<span data-ttu-id="06785-454">`Startup.Configure`의 경우</span><span class="sxs-lookup"><span data-stu-id="06785-454">In `Startup.Configure`:</span></span>

```csharp
//using Microsoft.AspNetCore.Diagnostics.HealthChecks;
//using Microsoft.Extensions.Diagnostics.HealthChecks;

app.UseHealthChecks("/health", new HealthCheckOptions()
{
    ResultStatusCodes =
    {
        [HealthStatus.Healthy] = StatusCodes.Status200OK,
        [HealthStatus.Degraded] = StatusCodes.Status200OK,
        [HealthStatus.Unhealthy] = StatusCodes.Status503ServiceUnavailable
    }
});
```

### <a name="suppress-cache-headers"></a><span data-ttu-id="06785-455">캐시 헤더 표시 안 함</span><span class="sxs-lookup"><span data-stu-id="06785-455">Suppress cache headers</span></span>

<span data-ttu-id="06785-456"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.AllowCachingResponses>는 응답 캐싱을 방지하기 위해 상태 검사 미들웨어가 HTTP 헤더를 프로브 응답에 추가할지 여부를 제어합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-456"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.AllowCachingResponses> controls whether the Health Checks Middleware adds HTTP headers to a probe response to prevent response caching.</span></span> <span data-ttu-id="06785-457">값이 `false`(기본값)인 경우 미들웨어는 응답 캐싱을 방지하기 위해 `Cache-Control`, `Expires` 및 `Pragma` 헤더를 설정하거나 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-457">If the value is `false` (default), the middleware sets or overrides the `Cache-Control`, `Expires`, and `Pragma` headers to prevent response caching.</span></span> <span data-ttu-id="06785-458">값이 `true`인 경우 미들웨어는 응답 캐시 헤더를 수정하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="06785-458">If the value is `true`, the middleware doesn't modify the cache headers of the response.</span></span>

<span data-ttu-id="06785-459">`Startup.Configure`의 경우</span><span class="sxs-lookup"><span data-stu-id="06785-459">In `Startup.Configure`:</span></span>

```csharp
//using Microsoft.AspNetCore.Diagnostics.HealthChecks;
//using Microsoft.Extensions.Diagnostics.HealthChecks;

app.UseHealthChecks("/health", new HealthCheckOptions()
{
    AllowCachingResponses = false
});
```

### <a name="customize-output"></a><span data-ttu-id="06785-460">출력 사용자 지정</span><span class="sxs-lookup"><span data-stu-id="06785-460">Customize output</span></span>

<span data-ttu-id="06785-461"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter> 옵션은 응답을 기록하는 데 사용되는 대리자를 가져오거나 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-461">The <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter> option gets or sets a delegate used to write the response.</span></span> <span data-ttu-id="06785-462">기본 대리자는 [HealthReport.Status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status)의 문자열 값을 사용하여 최소 일반 텍스트 응답을 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-462">The default delegate writes a minimal plaintext response with the string value of [HealthReport.Status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status).</span></span>

<span data-ttu-id="06785-463">`Startup.Configure`의 경우</span><span class="sxs-lookup"><span data-stu-id="06785-463">In `Startup.Configure`:</span></span>

```csharp
// using Microsoft.AspNetCore.Diagnostics.HealthChecks;
// using Microsoft.Extensions.Diagnostics.HealthChecks;

app.UseHealthChecks("/health", new HealthCheckOptions()
{
    ResponseWriter = WriteResponse
});
```

<span data-ttu-id="06785-464">기본 대리자는 [HealthReport.Status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status)의 문자열 값을 사용하여 최소 일반 텍스트 응답을 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-464">The default delegate writes a minimal plaintext response with the string value of [HealthReport.Status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status).</span></span> <span data-ttu-id="06785-465">다음 사용자 지정 대리자 `WriteResponse`는 사용자 지정 JSON 응답을 출력합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-465">The following custom delegate, `WriteResponse`, outputs a custom JSON response:</span></span>

```csharp
private static Task WriteResponse(HttpContext httpContext, HealthReport result)
{
    httpContext.Response.ContentType = "application/json";

    var json = new JObject(
        new JProperty("status", result.Status.ToString()),
        new JProperty("results", new JObject(result.Entries.Select(pair =>
            new JProperty(pair.Key, new JObject(
                new JProperty("status", pair.Value.Status.ToString()),
                new JProperty("description", pair.Value.Description),
                new JProperty("data", new JObject(pair.Value.Data.Select(
                    p => new JProperty(p.Key, p.Value))))))))));
    return httpContext.Response.WriteAsync(
        json.ToString(Formatting.Indented));
}
```

<span data-ttu-id="06785-466">상태 검사 시스템은 사용자가 선택한 모니터링 시스템에만 적용되는 형식이므로 복합 JSON 반환 형식에 대한 기본 제공 지원을 제공하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="06785-466">The health checks system doesn't provide built-in support for complex JSON return formats because the format is specific to your choice of monitoring system.</span></span> <span data-ttu-id="06785-467">필요에 따라 앞의 예제에서 `JObject`를 자유롭게 사용자 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="06785-467">Feel free to customize the `JObject` in the preceding example as necessary to meet your needs.</span></span>

## <a name="database-probe"></a><span data-ttu-id="06785-468">데이터베이스 프로브</span><span class="sxs-lookup"><span data-stu-id="06785-468">Database probe</span></span>

<span data-ttu-id="06785-469">상태 검사는 데이터베이스 쿼리를 지정하여 데이터베이스가 정상적으로 응답하는지를 나타내기 위해 부울 테스트로서 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="06785-469">A health check can specify a database query to run as a boolean test to indicate if the database is responding normally.</span></span>

<span data-ttu-id="06785-470">샘플 앱은 ASP.NET Core 앱의 상태 검사 라이브러리인 [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks)를 사용하여 SQL Server 데이터베이스에 대한 상태 검사를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-470">The sample app uses [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks), a health check library for ASP.NET Core apps, to perform a health check on a SQL Server database.</span></span> <span data-ttu-id="06785-471">`AspNetCore.Diagnostics.HealthChecks`는 데이터베이스에 연결이 양호한지 확인하기 위해 데이터베이스에 대해 `SELECT 1` 쿼리를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-471">`AspNetCore.Diagnostics.HealthChecks` executes a `SELECT 1` query against the database to confirm the connection to the database is healthy.</span></span>

> [!WARNING]
> <span data-ttu-id="06785-472">쿼리로 데이터베이스 연결을 검사할 때 신속하게 반환되는 쿼리를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-472">When checking a database connection with a query, choose a query that returns quickly.</span></span> <span data-ttu-id="06785-473">쿼리 방식 실행은 데이터베이스의 오버로드와 성능 저하를 유발할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="06785-473">The query approach runs the risk of overloading the database and degrading its performance.</span></span> <span data-ttu-id="06785-474">대부분의 경우 테스트 쿼리를 실행할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="06785-474">In most cases, running a test query isn't necessary.</span></span> <span data-ttu-id="06785-475">간단히 데이터베이스에 연결하는 정도로도 충분합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-475">Merely making a successful connection to the database is sufficient.</span></span> <span data-ttu-id="06785-476">쿼리를 실행해야 하는 경우 `SELECT 1`과 같은 간단한 SELECT 쿼리를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-476">If you find it necessary to run a query, choose a simple SELECT query, such as `SELECT 1`.</span></span>

<span data-ttu-id="06785-477">[AspNetCore.HealthChecks.SqlServer](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/)에 대한 패키지 참조를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-477">Include a package reference to [AspNetCore.HealthChecks.SqlServer](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/).</span></span>

<span data-ttu-id="06785-478">샘플 앱의 *appsettings.json* 파일에서 유효한 데이터베이스 연결 문자열을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-478">Supply a valid database connection string in the *appsettings.json* file of the sample app.</span></span> <span data-ttu-id="06785-479">앱은 `HealthCheckSample`이라고 하는 SQL Server 데이터베이스를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-479">The app uses a SQL Server database named `HealthCheckSample`:</span></span>

[!code-json[](health-checks/samples/2.x/HealthChecksSample/appsettings.json?highlight=3)]

<span data-ttu-id="06785-480">`Startup.ConfigureServices`의 <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*>에 상태 검사 서비스를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-480">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="06785-481">샘플 앱은 데이터베이스의 연결 문자열(*DbHealthStartup.cs*)을 사용하여 `AddSqlServer` 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-481">The sample app calls the `AddSqlServer` method with the database's connection string (*DbHealthStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/DbHealthStartup.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="06785-482">`Startup.Configure`의 앱 처리 파이프라인에서 상태 검사 미들웨어를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-482">Call Health Checks Middleware in the app processing pipeline in `Startup.Configure`:</span></span>

```csharp
app.UseHealthChecks("/health");
```

<span data-ttu-id="06785-483">샘플 앱을 사용하여 데이터베이스 프로브 시나리오를 실행하려면 명령 셸의 프로젝트 폴더에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-483">To run the database probe scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario db
```

> [!NOTE]
> <span data-ttu-id="06785-484">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks)는 Microsoft에서 유지 관리하거나 지원하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="06785-484">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

## <a name="entity-framework-core-dbcontext-probe"></a><span data-ttu-id="06785-485">Entity Framework Core DbContext 프로브</span><span class="sxs-lookup"><span data-stu-id="06785-485">Entity Framework Core DbContext probe</span></span>

<span data-ttu-id="06785-486">`DbContext` 검사는 앱이 EF Core `DbContext`에 대해 구성된 데이터베이스와 통신할 수 있음을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-486">The `DbContext` check confirms that the app can communicate with the database configured for an EF Core `DbContext`.</span></span> <span data-ttu-id="06785-487">`DbContext` 검사는 다음과 같은 앱에서 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="06785-487">The `DbContext` check is supported in apps that:</span></span>

* <span data-ttu-id="06785-488">[EF(Entity Framework) Core](/ef/core/)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-488">Use [Entity Framework (EF) Core](/ef/core/).</span></span>
* <span data-ttu-id="06785-489">[Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/)에 대한 패키지 참조를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-489">Include a package reference to [Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/).</span></span>

<span data-ttu-id="06785-490">`AddDbContextCheck<TContext>`는 `DbContext`에 대한 상태 검사를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-490">`AddDbContextCheck<TContext>` registers a health check for a `DbContext`.</span></span> <span data-ttu-id="06785-491">`DbContext`는 메서드에 `TContext`로 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="06785-491">The `DbContext` is supplied as the `TContext` to the method.</span></span> <span data-ttu-id="06785-492">오버로드는 오류 상태, 태그 및 사용자 지정 테스트 쿼리를 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="06785-492">An overload is available to configure the failure status, tags, and a custom test query.</span></span>

<span data-ttu-id="06785-493">기본적으로 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="06785-493">By default:</span></span>

* <span data-ttu-id="06785-494">`DbContextHealthCheck`는 EF Core의 `CanConnectAsync` 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-494">The `DbContextHealthCheck` calls EF Core's `CanConnectAsync` method.</span></span> <span data-ttu-id="06785-495">`AddDbContextCheck` 메서드 오버로드를 사용하여 상태를 검사할 때 실행되는 작업을 사용자 정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="06785-495">You can customize what operation is run when checking health using `AddDbContextCheck` method overloads.</span></span>
* <span data-ttu-id="06785-496">상태 검사의 이름은 `TContext` 형식의 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="06785-496">The name of the health check is the name of the `TContext` type.</span></span>

<span data-ttu-id="06785-497">샘플 앱에서 `AppDbContext`는 `AddDbContextCheck`에 제공되고 `Startup.ConfigureServices`의 서비스로서 등록됩니다(*DbContextHealthStartup.cs*).</span><span class="sxs-lookup"><span data-stu-id="06785-497">In the sample app, `AppDbContext` is provided to `AddDbContextCheck` and registered as a service in `Startup.ConfigureServices` (*DbContextHealthStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/DbContextHealthStartup.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="06785-498">샘플 앱에서 `UseHealthChecks`는 `Startup.Configure`에서 상태 검사 미들웨어를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-498">In the sample app, `UseHealthChecks` adds the Health Checks Middleware in `Startup.Configure`.</span></span>

```csharp
app.UseHealthChecks("/health");
```

<span data-ttu-id="06785-499">샘플 앱을 사용하여 `DbContext` 프로브 시나리오를 실행하려면 연결 문자열로 지정된 데이터베이스가 SQL Server 인스턴스에 있지 않은지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-499">To run the `DbContext` probe scenario using the sample app, confirm that the database specified by the connection string doesn't exist in the SQL Server instance.</span></span> <span data-ttu-id="06785-500">데이터베이스가 있으면 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-500">If the database exists, delete it.</span></span>

<span data-ttu-id="06785-501">명령 셸의 프로젝트 폴더에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-501">Execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario dbcontext
```

<span data-ttu-id="06785-502">앱을 실행한 후 브라우저의 `/health` 엔드포인트에 요청하여 상태를 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-502">After the app is running, check the health status by making a request to the `/health` endpoint in a browser.</span></span> <span data-ttu-id="06785-503">데이터베이스 및 `AppDbContext`가 존재하기 않기 때문에 앱은 다음 응답을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-503">The database and `AppDbContext` don't exist, so app provides the following response:</span></span>

```
Unhealthy
```

<span data-ttu-id="06785-504">데이터베이스를 만드는 샘플 앱을 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-504">Trigger the sample app to create the database.</span></span> <span data-ttu-id="06785-505">`/createdatabase`에 요청합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-505">Make a request to `/createdatabase`.</span></span> <span data-ttu-id="06785-506">앱은 다음과 같이 응답합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-506">The app responds:</span></span>

```
Creating the database...
Done!
Navigate to /health to see the health status.
```

<span data-ttu-id="06785-507">`/health` 엔드포인트에 요청합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-507">Make a request to the `/health` endpoint.</span></span> <span data-ttu-id="06785-508">데이터베이스와 컨텍스트가 존재하기 때문에 앱은 다음과 같이 응답합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-508">The database and context exist, so app responds:</span></span>

```
Healthy
```

<span data-ttu-id="06785-509">데이터베이스를 삭제하는 샘플 앱을 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-509">Trigger the sample app to delete the database.</span></span> <span data-ttu-id="06785-510">`/deletedatabase`에 요청합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-510">Make a request to `/deletedatabase`.</span></span> <span data-ttu-id="06785-511">앱은 다음과 같이 응답합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-511">The app responds:</span></span>

```
Deleting the database...
Done!
Navigate to /health to see the health status.
```

<span data-ttu-id="06785-512">`/health` 엔드포인트에 요청합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-512">Make a request to the `/health` endpoint.</span></span> <span data-ttu-id="06785-513">앱은 다음과 같이 비정상 응답을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-513">The app provides an unhealthy response:</span></span>

```
Unhealthy
```

## <a name="separate-readiness-and-liveness-probes"></a><span data-ttu-id="06785-514">별도의 준비 상태 및 활동성 프로브</span><span class="sxs-lookup"><span data-stu-id="06785-514">Separate readiness and liveness probes</span></span>

<span data-ttu-id="06785-515">일부 호스팅 시나리오에서 두 개의 앱 상태를 구분하는 한 쌍의 상태 검사가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="06785-515">In some hosting scenarios, a pair of health checks are used that distinguish two app states:</span></span>

* <span data-ttu-id="06785-516">앱은 작동하지만, 아직 요청을 받을 준비가 되지 않았습니다.</span><span class="sxs-lookup"><span data-stu-id="06785-516">The app is functioning but not yet ready to receive requests.</span></span> <span data-ttu-id="06785-517">이 상태는 앱의 *준비 상태*입니다.</span><span class="sxs-lookup"><span data-stu-id="06785-517">This state is the app's *readiness*.</span></span>
* <span data-ttu-id="06785-518">앱을 작동하고 요청에 응답합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-518">The app is functioning and responding to requests.</span></span> <span data-ttu-id="06785-519">이 상태는 앱의 *활동성*입니다.</span><span class="sxs-lookup"><span data-stu-id="06785-519">This state is the app's *liveness*.</span></span>

<span data-ttu-id="06785-520">준비 상태 검사는 대개 앱의 하위 시스템 및 리소스를 모두 사용할 수 있는지 확인하기 위해 보다 광범위하고 시간이 많이 소요되는 검사 세트를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-520">The readiness check usually performs a more extensive and time-consuming set of checks to determine if all of the app's subsystems and resources are available.</span></span> <span data-ttu-id="06785-521">활동성 검사는 앱이 요청을 처리할 수 있는지 여부를 확인하기 위해 빠른 검사만 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-521">A liveness check merely performs a quick check to determine if the app is available to process requests.</span></span> <span data-ttu-id="06785-522">앱이 준비 상태 검사를 통과한 후에는 값 비싼 준비 상태 검사 세트로 앱에 추가로 부담 지울 필요가 없으며&mdash;이후 활동성에 대한 검사만 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-522">After the app passes its readiness check, there's no need to burden the app further with the expensive set of readiness checks&mdash;further checks only require checking for liveness.</span></span>

<span data-ttu-id="06785-523">샘플 앱에는 [호스팅된 서비스](xref:fundamentals/host/hosted-services)에서 장기 실행 시작 작업 완료를 보고하는 상태 검사가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="06785-523">The sample app contains a health check to report the completion of long-running startup task in a [Hosted Service](xref:fundamentals/host/hosted-services).</span></span> <span data-ttu-id="06785-524">`StartupHostedServiceHealthCheck`는 장기 실행 작업이 완료되면(*StartupHostedServiceHealthCheck.cs*) 호스팅된 서비스를 `true`로 설정할 수 있는 속성 `StartupTaskCompleted`를 노출합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-524">The `StartupHostedServiceHealthCheck` exposes a property, `StartupTaskCompleted`, that the hosted service can set to `true` when its long-running task is finished (*StartupHostedServiceHealthCheck.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/StartupHostedServiceHealthCheck.cs?name=snippet1&highlight=7-11)]

<span data-ttu-id="06785-525">장기 실행 백그라운드 작업은 [호스팅된 서비스](xref:fundamentals/host/hosted-services)(*Services/StartupHostedService*)에 의해 시작됩니다.</span><span class="sxs-lookup"><span data-stu-id="06785-525">The long-running background task is started by a [Hosted Service](xref:fundamentals/host/hosted-services) (*Services/StartupHostedService*).</span></span> <span data-ttu-id="06785-526">작업이 끝나면 `StartupHostedServiceHealthCheck.StartupTaskCompleted`는 `true`로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="06785-526">At the conclusion of the task, `StartupHostedServiceHealthCheck.StartupTaskCompleted` is set to `true`:</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/Services/StartupHostedService.cs?name=snippet1&highlight=18-20)]

<span data-ttu-id="06785-527">상태 검사는 호스팅된 서비스와 함께 `Startup.ConfigureServices`의 <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>에 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="06785-527">The health check is registered with <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> in `Startup.ConfigureServices` along with the hosted service.</span></span> <span data-ttu-id="06785-528">호스팅된 서비스가 상태 검사에서 속성을 설정해야 하기 때문에 상태 확인도 서비스 컨테이너(*LivenessProbeStartup.cs*)에 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="06785-528">Because the hosted service must set the property on the health check, the health check is also registered in the service container (*LivenessProbeStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="06785-529">`Startup.Configure`의 앱 처리 파이프라인에서 상태 검사 미들웨어를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-529">Call Health Checks Middleware in the app processing pipeline in `Startup.Configure`.</span></span> <span data-ttu-id="06785-530">샘플 앱에서 상태 검사 엔드포인트는 활동성 검사의 경우 `/health/ready`에, 준비 상태 검사의 경우 `/health/live`에 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="06785-530">In the sample app, the health check endpoints are created at `/health/ready` for the readiness check and `/health/live` for the liveness check.</span></span> <span data-ttu-id="06785-531">준비 상태 검사는 `ready` 태그가 있는 상태 검사에서 상태 검사를 필터링합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-531">The readiness check filters health checks to the health check with the `ready` tag.</span></span> <span data-ttu-id="06785-532">활동성 검사는 [HealthCheckOptions.Predicate](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate)에서 `false`를 반환하여 `StartupHostedServiceHealthCheck`를 필터링합니다(자세한 내용은 [상태 검사 필터링](#filter-health-checks) 참조).</span><span class="sxs-lookup"><span data-stu-id="06785-532">The liveness check filters out the `StartupHostedServiceHealthCheck` by returning `false` in the [HealthCheckOptions.Predicate](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate) (for more information, see [Filter health checks](#filter-health-checks)):</span></span>

```csharp
app.UseHealthChecks("/health/ready", new HealthCheckOptions()
{
    Predicate = (check) => check.Tags.Contains("ready"), 
});

app.UseHealthChecks("/health/live", new HealthCheckOptions()
{
    Predicate = (_) => false
});
```

<span data-ttu-id="06785-533">샘플 앱을 사용하여 준비 상태/활동성 구성 시나리오를 실행하려면 명령 셸의 프로젝트 폴더에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-533">To run the readiness/liveness configuration scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario liveness
```

<span data-ttu-id="06785-534">브라우저에서 15초가 경과할 때까지 `/health/ready`를 여러 번 방문합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-534">In a browser, visit `/health/ready` several times until 15 seconds have passed.</span></span> <span data-ttu-id="06785-535">상태 검사는 첫 15초 동안 *Unhealthy*를 보고합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-535">The health check reports *Unhealthy* for the first 15 seconds.</span></span> <span data-ttu-id="06785-536">15초 후, 엔드포인트는 *Healthy*를 보고하여 호스트된 서비스에 의해 장기 실행 작업이 완료되었음을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="06785-536">After 15 seconds, the endpoint reports *Healthy*, which reflects the completion of the long-running task by the hosted service.</span></span>

<span data-ttu-id="06785-537">이 예제에서는 2초 지연을 사용하여 첫 번째 준비 검사를 실행하는 상태 검사 게시자(<xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> 구현)도 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="06785-537">This example also creates a Health Check Publisher (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation) that runs the first readiness check with a two second delay.</span></span> <span data-ttu-id="06785-538">자세한 내용은 [상태 검사 게시자](#health-check-publisher) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="06785-538">For more information, see the [Health Check Publisher](#health-check-publisher) section.</span></span>

### <a name="kubernetes-example"></a><span data-ttu-id="06785-539">Kubernetes 예제</span><span class="sxs-lookup"><span data-stu-id="06785-539">Kubernetes example</span></span>

<span data-ttu-id="06785-540">별도의 준비 상태 및 활동성 검사를 사용하는 것은 [Kubernetes](https://kubernetes.io/)와 같은 환경에서 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-540">Using separate readiness and liveness checks is useful in an environment such as [Kubernetes](https://kubernetes.io/).</span></span> <span data-ttu-id="06785-541">Kubernetes에서 앱은 기본 데이터베이스 가용성 테스트와 같이 요청을 수락하기 전에 시간이 많이 걸리는 시작 작업을 수행해야 할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="06785-541">In Kubernetes, an app might be required to perform time-consuming startup work before accepting requests, such as a test of the underlying database availability.</span></span> <span data-ttu-id="06785-542">별도의 검사를 사용하면 오케스트레이터가 앱이 작동하고 있지만 아직 준비가 되지 않았는지 또는 앱이 시작되지 않았는지 구분할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="06785-542">Using separate checks allows the orchestrator to distinguish whether the app is functioning but not yet ready or if the app has failed to start.</span></span> <span data-ttu-id="06785-543">Kubernetes의 준비 상태 및 활동성 프로브에 대한 자세한 내용은 Kubernetes 설명서의 [활동성 및 준비 상태 프로브 구성](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="06785-543">For more information on readiness and liveness probes in Kubernetes, see [Configure Liveness and Readiness Probes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) in the Kubernetes documentation.</span></span>

<span data-ttu-id="06785-544">다음 예제는 Kubernetes 준비 상태 프로브 구성을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="06785-544">The following example demonstrates a Kubernetes readiness probe configuration:</span></span>

```
spec:
  template:
  spec:
    readinessProbe:
      # an http probe
      httpGet:
        path: /health/ready
        port: 80
      # length of time to wait for a pod to initialize
      # after pod startup, before applying health checking
      initialDelaySeconds: 30
      timeoutSeconds: 1
    ports:
      - containerPort: 80
```

## <a name="metric-based-probe-with-a-custom-response-writer"></a><span data-ttu-id="06785-545">사용자 지정 응답 기록기가 있는 메트릭 기반 프로브</span><span class="sxs-lookup"><span data-stu-id="06785-545">Metric-based probe with a custom response writer</span></span>

<span data-ttu-id="06785-546">샘플 앱은 사용자 지정 응답 기록기가 있는 메모리 상태를 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="06785-546">The sample app demonstrates a memory health check with a custom response writer.</span></span>

<span data-ttu-id="06785-547">앱이 일정한 메모리 임계값(샘플 앱의 경우 1GB)을 초과하여 사용하는 경우 `MemoryHealthCheck`는 비정상적인 상태를 보고합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-547">`MemoryHealthCheck` reports an unhealthy status if the app uses more than a given threshold of memory (1 GB in the sample app).</span></span> <span data-ttu-id="06785-548"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult>에는 앱의 가비지 수집기(GC) 정보가 포함됩니다(*MemoryHealthCheck.cs*).</span><span class="sxs-lookup"><span data-stu-id="06785-548">The <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> includes Garbage Collector (GC) information for the app (*MemoryHealthCheck.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/MemoryHealthCheck.cs?name=snippet1)]

<span data-ttu-id="06785-549">`Startup.ConfigureServices`의 <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*>에 상태 검사 서비스를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-549">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="06785-550"><xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>에 전달하여 상태 검사를 사용하는 대신 `MemoryHealthCheck`는 서비스로 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="06785-550">Instead of enabling the health check by passing it to <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>, the `MemoryHealthCheck` is registered as a service.</span></span> <span data-ttu-id="06785-551">모든 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> 등록된 서비스는 상태 검사 서비스 및 미들웨어에 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="06785-551">All <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> registered services are available to the health check services and middleware.</span></span> <span data-ttu-id="06785-552">상태 확인 서비스를 Singleton 서비스로 등록하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="06785-552">We recommend registering health check services as Singleton services.</span></span>

<span data-ttu-id="06785-553">샘플 앱(*CustomWriterStartup.cs*)에서:</span><span class="sxs-lookup"><span data-stu-id="06785-553">In the sample app (*CustomWriterStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_ConfigureServices&highlight=4)]

<span data-ttu-id="06785-554">`Startup.Configure`의 앱 처리 파이프라인에서 상태 검사 미들웨어를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-554">Call Health Checks Middleware in the app processing pipeline in `Startup.Configure`.</span></span> <span data-ttu-id="06785-555">상태 검사가 실행될 때 사용자 정의 JSON 응답을 출력하기 위해 `ResponseWriter` 속성에 `WriteResponse` 대리자가 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="06785-555">A `WriteResponse` delegate is provided to the `ResponseWriter` property to output a custom JSON response when the health check executes:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseHealthChecks("/health", new HealthCheckOptions()
    {
        // This custom writer formats the detailed status as JSON.
        ResponseWriter = WriteResponse
    });
}
```

<span data-ttu-id="06785-556">`WriteResponse` 메서드는 `CompositeHealthCheckResult`를 JSON 객체로 형식 지정하고 상태 검사 응답을 위해 JSON 출력을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-556">The `WriteResponse` method formats the `CompositeHealthCheckResult` into a JSON object and yields JSON output for the health check response:</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_WriteResponse)]

<span data-ttu-id="06785-557">샘플 앱을 사용하여 사용자 지정 응답 기록기 출력으로 메트릭 기반 프로브를 실행하려면 명령 셸의 프로젝트 폴더에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-557">To run the metric-based probe with custom response writer output using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario writer
```

> [!NOTE]
> <span data-ttu-id="06785-558">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks)에는 디스크 스토리지 및 최댓값 활동성 검사를 포함하여 메트릭 기반 상태 확인 시나리오가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="06785-558">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) includes metric-based health check scenarios, including disk storage and maximum value liveness checks.</span></span>
>
> <span data-ttu-id="06785-559">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks)는 Microsoft에서 유지 관리하거나 지원하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="06785-559">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

## <a name="filter-by-port"></a><span data-ttu-id="06785-560">포트별 필터링</span><span class="sxs-lookup"><span data-stu-id="06785-560">Filter by port</span></span>

<span data-ttu-id="06785-561">포트와 함께 <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*>를 호출하면 지정된 포트에 대한 상태 검사 요청을 제한합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-561">Calling <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> with a port restricts health check requests to the port specified.</span></span> <span data-ttu-id="06785-562">이는 일반적으로 컨테이너 환경에서 서비스 모니터링을 위해 포트를 노출하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="06785-562">This is typically used in a container environment to expose a port for monitoring services.</span></span>

<span data-ttu-id="06785-563">샘플 앱은 [환경 변수 구성 공급자](xref:fundamentals/configuration/index#environment-variables-configuration-provider)를 사용하여 포트를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-563">The sample app configures the port using the [Environment Variable Configuration Provider](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span></span> <span data-ttu-id="06785-564">포트는 *launchSettings.json* 파일에서 설정되고 환경 변수를 통해 구성 공급자에게 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="06785-564">The port is set in the *launchSettings.json* file and passed to the configuration provider via an environment variable.</span></span> <span data-ttu-id="06785-565">또한 관리 포트에서 요청을 수신하도록 서버를 구성해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-565">You must also configure the server to listen to requests on the management port.</span></span>

<span data-ttu-id="06785-566">샘플 앱을 사용하여 관리 포트 구성을 보여주려면 *launchSettings.json* 파일을 *Properties* 폴더에 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="06785-566">To use the sample app to demonstrate management port configuration, create the *launchSettings.json* file in a *Properties* folder.</span></span>

<span data-ttu-id="06785-567">샘플 앱의 다음 *Properties/launchSettings.json* 파일은 샘플 앱의 프로젝트 파일에 포함되지 않으며 수동으로 만들어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-567">The following *Properties/launchSettings.json* file in the sample app isn't included in the sample app's project files and must be created manually:</span></span>

```json
{
  "profiles": {
    "SampleApp": {
      "commandName": "Project",
      "commandLineArgs": "",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development",
        "ASPNETCORE_URLS": "http://localhost:5000/;http://localhost:5001/",
        "ASPNETCORE_MANAGEMENTPORT": "5001"
      },
      "applicationUrl": "http://localhost:5000/"
    }
  }
}
```

<span data-ttu-id="06785-568">`Startup.ConfigureServices`의 <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*>에 상태 검사 서비스를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-568">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="06785-569"><xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*>에 대한 호출은 관리 포트를 지정합니다(*ManagementPortStartup.cs*).</span><span class="sxs-lookup"><span data-stu-id="06785-569">The call to <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> specifies the management port (*ManagementPortStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/ManagementPortStartup.cs?name=snippet1&highlight=17)]

> [!NOTE]
> <span data-ttu-id="06785-570">코드에서 URL과 관리 포트를 명시적으로 설정하여 샘플 앱에 *launchSettings.json* 파일을 만들지 않도록 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="06785-570">You can avoid creating the *launchSettings.json* file in the sample app by setting the URLs and management port explicitly in code.</span></span> <span data-ttu-id="06785-571"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilder>가 만들어진 *Program.cs*에서 <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>에 호출을 추가하고 앱의 정상적인 응답 엔드포인트와 관리 포트 엔드포인트를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-571">In *Program.cs* where the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> is created, add a call to <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*> and provide the app's normal response endpoint and the management port endpoint.</span></span> <span data-ttu-id="06785-572"><xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*>가 호출되는 *ManagementPortStartup.cs*에서 관리 포트를 명시적으로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-572">In *ManagementPortStartup.cs* where <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> is called, specify the management port explicitly.</span></span>
>
> <span data-ttu-id="06785-573">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="06785-573">*Program.cs*:</span></span>
>
> ```csharp
> return new WebHostBuilder()
>     .UseConfiguration(config)
>     .UseUrls("http://localhost:5000/;http://localhost:5001/")
>     .ConfigureLogging(builder =>
>     {
>         builder.SetMinimumLevel(LogLevel.Trace);
>         builder.AddConfiguration(config);
>         builder.AddConsole();
>     })
>     .UseKestrel()
>     .UseStartup(startupType)
>     .Build();
> ```
>
> <span data-ttu-id="06785-574">*ManagementPortStartup.cs*:</span><span class="sxs-lookup"><span data-stu-id="06785-574">*ManagementPortStartup.cs*:</span></span>
>
> ```csharp
> app.UseHealthChecks("/health", port: 5001);
> ```

<span data-ttu-id="06785-575">샘플 앱을 사용하여 관리 포트 구성 시나리오를 실행하려면 명령 셸의 프로젝트 폴더에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-575">To run the management port configuration scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario port
```

## <a name="distribute-a-health-check-library"></a><span data-ttu-id="06785-576">상태 검사 라이브러리 배포</span><span class="sxs-lookup"><span data-stu-id="06785-576">Distribute a health check library</span></span>

<span data-ttu-id="06785-577">상태 검사를 라이브러리로 배포하려면 다음과 같이 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-577">To distribute a health check as a library:</span></span>

1. <span data-ttu-id="06785-578"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> 인터페이스를 독립 실행형 클래스로 구현하는 상태 검사를 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-578">Write a health check that implements the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> interface as a standalone class.</span></span> <span data-ttu-id="06785-579">클래스는 [DI(종속성 주입)](xref:fundamentals/dependency-injection), 유형 활성화 및 [명명된 옵션](xref:fundamentals/configuration/options)에 의존하여 구성 데이터에 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="06785-579">The class can rely on [dependency injection (DI)](xref:fundamentals/dependency-injection), type activation, and [named options](xref:fundamentals/configuration/options) to access configuration data.</span></span>

   <span data-ttu-id="06785-580">`CheckHealthAsync`의 상태 검사 논리에서:</span><span class="sxs-lookup"><span data-stu-id="06785-580">In the health checks logic of `CheckHealthAsync`:</span></span>

   * <span data-ttu-id="06785-581">`data1` 및 `data2`는 메서드에서 프로브의 상태 검사 논리를 실행하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="06785-581">`data1` and `data2` are used in the method to run the probe's health check logic.</span></span>
   * <span data-ttu-id="06785-582">`AccessViolationException`이 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="06785-582">`AccessViolationException` is handled.</span></span>

   <span data-ttu-id="06785-583"><xref:System.AccessViolationException>이 발생하는 경우 사용자가 상태 검사 실패 상태를 구성할 수 있도록 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.FailureStatus>가 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult>와 함께 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="06785-583">When an <xref:System.AccessViolationException> occurs, the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.FailureStatus> is returned with the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> to allow users to configure the health checks failure status.</span></span>

   ```csharp
   using System;
   using System.Threading;
   using System.Threading.Tasks;
   using Microsoft.Extensions.Diagnostics.HealthChecks;

   public class ExampleHealthCheck : IHealthCheck
   {
       private readonly string _data1;
       private readonly int? _data2;

       public ExampleHealthCheck(string data1, int? data2)
       {
           _data1 = data1 ?? throw new ArgumentNullException(nameof(data1));
           _data2 = data2 ?? throw new ArgumentNullException(nameof(data2));
       }

       public async Task<HealthCheckResult> CheckHealthAsync(
           HealthCheckContext context, CancellationToken cancellationToken)
       {
           try
           {
               return HealthCheckResult.Healthy();
           }
           catch (AccessViolationException ex)
           {
               return new HealthCheckResult(
                   context.Registration.FailureStatus,
                   description: "An access violation occurred during the check.",
                   exception: ex,
                   data: null);
           }
       }
   }
   ```

1. <span data-ttu-id="06785-584">사용하는 앱이 `Startup.Configure` 메서드에서 호출하는 매개 변수를 사용하여 확장 메서드를 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-584">Write an extension method with parameters that the consuming app calls in its `Startup.Configure` method.</span></span> <span data-ttu-id="06785-585">다음 예제에서는 다음 상태 검사 메서드 서명을 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-585">In the following example, assume the following health check method signature:</span></span>

   ```csharp
   ExampleHealthCheck(string, string, int )
   ```

   <span data-ttu-id="06785-586">이전 서명은 `ExampleHealthCheck`가 상태 검사 프로브 로직을 처리하기 위해 추가 데이터가 필요함을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="06785-586">The preceding signature indicates that the `ExampleHealthCheck` requires additional data to process the health check probe logic.</span></span> <span data-ttu-id="06785-587">상태 검사가 확장 메서드를 사용하여 등록되면 데이터는 상태 검사 인스턴스를 만드는 데 사용된 대리자에게 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="06785-587">The data is provided to the delegate used to create the health check instance when the health check is registered with an extension method.</span></span> <span data-ttu-id="06785-588">다음 예제에서 호출자는 선택 사항을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-588">In the following example, the caller specifies optional:</span></span>

   * <span data-ttu-id="06785-589">상태 검사 이름(`name`).</span><span class="sxs-lookup"><span data-stu-id="06785-589">health check name (`name`).</span></span> <span data-ttu-id="06785-590">`null`인 경우 `example_health_check`가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="06785-590">If `null`, `example_health_check` is used.</span></span>
   * <span data-ttu-id="06785-591">상태 검사를 위한 문자열 데이터 요소입니다(`data1`).</span><span class="sxs-lookup"><span data-stu-id="06785-591">string data point for the health check (`data1`).</span></span>
   * <span data-ttu-id="06785-592">상태 검사를 위한 정수 데이터 요소입니다(`data2`).</span><span class="sxs-lookup"><span data-stu-id="06785-592">integer data point for the health check (`data2`).</span></span> <span data-ttu-id="06785-593">`null`인 경우 `1`이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="06785-593">If `null`, `1` is used.</span></span>
   * <span data-ttu-id="06785-594">실패 상태입니다(<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>).</span><span class="sxs-lookup"><span data-stu-id="06785-594">failure status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>).</span></span> <span data-ttu-id="06785-595">기본값은 `null`입니다.</span><span class="sxs-lookup"><span data-stu-id="06785-595">The default is `null`.</span></span> <span data-ttu-id="06785-596">`null`인 경우 오류 상태는 [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus)로 보고됩니다.</span><span class="sxs-lookup"><span data-stu-id="06785-596">If `null`, [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) is reported for a failure status.</span></span>
   * <span data-ttu-id="06785-597">태그(`IEnumerable<string>`)입니다.</span><span class="sxs-lookup"><span data-stu-id="06785-597">tags (`IEnumerable<string>`).</span></span>

   ```csharp
   using System.Collections.Generic;
   using Microsoft.Extensions.Diagnostics.HealthChecks;

   public static class ExampleHealthCheckBuilderExtensions
   {
       const string DefaultName = "example_health_check";

       public static IHealthChecksBuilder AddExampleHealthCheck(
           this IHealthChecksBuilder builder,
           string name = default,
           string data1,
           int data2 = 1,
           HealthStatus? failureStatus = default,
           IEnumerable<string> tags = default)
       {
           return builder.Add(new HealthCheckRegistration(
               name ?? DefaultName,
               sp => new ExampleHealthCheck(data1, data2),
               failureStatus,
               tags));
       }
   }
   ```

## <a name="health-check-publisher"></a><span data-ttu-id="06785-598">상태 검사 게시자</span><span class="sxs-lookup"><span data-stu-id="06785-598">Health Check Publisher</span></span>

<span data-ttu-id="06785-599"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher>가 서비스 컨테이너에 추가되면 상태 검사 시스템이 주기적으로 상태 검사를 실행하고 그 결과로 `PublishAsync`를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-599">When an <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> is added to the service container, the health check system periodically executes your health checks and calls `PublishAsync` with the result.</span></span> <span data-ttu-id="06785-600">이는 각 프로세스가 상태를 결정하기 위해 주기적으로 모니터링 시스템을 호출하도록 하는 푸시 기반 상태 모니터링 시스템 시나리오에서 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-600">This is useful in a push-based health monitoring system scenario that expects each process to call the monitoring system periodically in order to determine health.</span></span>

<span data-ttu-id="06785-601"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> 인터페이스는 단일 메서드를 가집니다.</span><span class="sxs-lookup"><span data-stu-id="06785-601">The <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> interface has a single method:</span></span>

```csharp
Task PublishAsync(HealthReport report, CancellationToken cancellationToken);
```

<span data-ttu-id="06785-602"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions>를 사용하여 다음을 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="06785-602"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions> allow you to set:</span></span>

* <span data-ttu-id="06785-603"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay> &ndash; 앱이 시작된 후 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> 인스턴스를 시작하기 전에 적용되는 초기 지연입니다.</span><span class="sxs-lookup"><span data-stu-id="06785-603"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay> &ndash; The initial delay applied after the app starts before executing <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instances.</span></span> <span data-ttu-id="06785-604">지연은 시작 시 한 번 적용되며 후속 반복에는 적용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="06785-604">The delay is applied once at startup and doesn't apply to subsequent iterations.</span></span> <span data-ttu-id="06785-605">기본값은 5초입니다.</span><span class="sxs-lookup"><span data-stu-id="06785-605">The default value is five seconds.</span></span>
* <span data-ttu-id="06785-606"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period> &ndash; <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> 실행 기간입니다.</span><span class="sxs-lookup"><span data-stu-id="06785-606"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period> &ndash; The period of <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> execution.</span></span> <span data-ttu-id="06785-607">기본값은 30초입니다.</span><span class="sxs-lookup"><span data-stu-id="06785-607">The default value is 30 seconds.</span></span>
* <span data-ttu-id="06785-608"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> &ndash; <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate>가 `null`(기본값)인 경우 상태 검사 게시자 서비스는 등록된 상태 검사를 모두 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-608"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> &ndash; If <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> is `null` (default), the health check publisher service runs all registered health checks.</span></span> <span data-ttu-id="06785-609">상태 검사 하위 집합을 실행하려면 검사 세트를 필터링하는 함수를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-609">To run a subset of health checks, provide a function that filters the set of checks.</span></span> <span data-ttu-id="06785-610">조건자는 기간마다 평가됩니다.</span><span class="sxs-lookup"><span data-stu-id="06785-610">The predicate is evaluated each period.</span></span>
* <span data-ttu-id="06785-611"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Timeout> &ndash; 모든 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> 인스턴스에 대한 상태 검사 실행의 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="06785-611"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Timeout> &ndash; The timeout for executing the health checks for all <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instances.</span></span> <span data-ttu-id="06785-612">시간 제한 없이 실행하려면 <xref:System.Threading.Timeout.InfiniteTimeSpan>을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-612">Use <xref:System.Threading.Timeout.InfiniteTimeSpan> to execute without a timeout.</span></span> <span data-ttu-id="06785-613">기본값은 30초입니다.</span><span class="sxs-lookup"><span data-stu-id="06785-613">The default value is 30 seconds.</span></span>

> [!WARNING]
> <span data-ttu-id="06785-614">ASP.NET Core 2.2 릴리스에서 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> 구현은 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period> 설정을 적용하지 않으며, <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay> 값을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-614">In the ASP.NET Core 2.2 release, setting <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period> isn't honored by the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation; it sets the value of <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay>.</span></span> <span data-ttu-id="06785-615">이 문제는 ASP.NET Core 3.0에서 해결되었습니다.</span><span class="sxs-lookup"><span data-stu-id="06785-615">This issue has been addressed in ASP.NET Core 3.0.</span></span>

<span data-ttu-id="06785-616">샘플 앱에서 `ReadinessPublisher`는 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> 구현입니다.</span><span class="sxs-lookup"><span data-stu-id="06785-616">In the sample app, `ReadinessPublisher` is an <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation.</span></span> <span data-ttu-id="06785-617">상태 검사 상태는 다음의 로그 수준에서 각 검사에 대해 기록됩니다.</span><span class="sxs-lookup"><span data-stu-id="06785-617">The health check status is logged for each check as either:</span></span>

* <span data-ttu-id="06785-618">상태 검사 상태가 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Healthy>인 경우 정보(<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*>)</span><span class="sxs-lookup"><span data-stu-id="06785-618">Information (<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*>) if the health checks status is <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Healthy>.</span></span>
* <span data-ttu-id="06785-619">상태가 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Degraded> 또는 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Unhealthy>인 경우 오류(<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError*>)</span><span class="sxs-lookup"><span data-stu-id="06785-619">Error (<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError*>) if the status is either <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Degraded> or <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Unhealthy>.</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/ReadinessPublisher.cs?name=snippet_ReadinessPublisher&highlight=18-27)]

<span data-ttu-id="06785-620">샘플 앱의 `LivenessProbeStartup` 예제에서 `StartupHostedService` 준비 검사에는 2초 시작 지연이 있고 30초마다 검사가 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="06785-620">In the sample app's `LivenessProbeStartup` example, the `StartupHostedService` readiness check has a two second startup delay and runs the check every 30 seconds.</span></span> <span data-ttu-id="06785-621"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> 구현을 활성화하기 위해 샘플은 [DI(종속성 주입)](xref:fundamentals/dependency-injection) 컨테이너에서 `ReadinessPublisher`를 싱글톤 서비스로 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-621">To activate the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation, the sample registers `ReadinessPublisher` as a singleton service in the [dependency injection (DI)](xref:fundamentals/dependency-injection) container:</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_ConfigureServices&highlight=12-17,28)]

> [!NOTE]
> <span data-ttu-id="06785-622">다음 해결 방법에서는 하나 이상의 다른 호스트 서비스가 이미 앱에 추가되었을 때 서비스 컨테이너에 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> 인스턴스를 추가하는 것을 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-622">The following workaround permits adding an <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instance to the service container when one or more other hosted services have already been added to the app.</span></span> <span data-ttu-id="06785-623">ASP.NET Core 3.0에는 이 해결 방법이 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="06785-623">This workaround won't isn't required in ASP.NET Core 3.0.</span></span>
>
> ```csharp
> private const string HealthCheckServiceAssembly =
>     "Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherHostedService";
>
> services.TryAddEnumerable(
>     ServiceDescriptor.Singleton(typeof(IHostedService),
>         typeof(HealthCheckPublisherOptions).Assembly
>             .GetType(HealthCheckServiceAssembly)));
> ```

> [!NOTE]
> <span data-ttu-id="06785-624">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks)는 [Application Insights](/azure/application-insights/app-insights-overview)를 포함하여 몇몇 시스템에 대한 게시자를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-624">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) includes publishers for several systems, including [Application Insights](/azure/application-insights/app-insights-overview).</span></span>
>
> <span data-ttu-id="06785-625">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks)는 Microsoft에서 유지 관리하거나 지원하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="06785-625">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

## <a name="restrict-health-checks-with-mapwhen"></a><span data-ttu-id="06785-626">MapWhen으로 상태 검사 제한</span><span class="sxs-lookup"><span data-stu-id="06785-626">Restrict health checks with MapWhen</span></span>

<span data-ttu-id="06785-627"><xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*>을 사용하여 상태 검사 엔드포인트에 대한 요청 파이프라인을 조건부로 분기합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-627">Use <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> to conditionally branch the request pipeline for health check endpoints.</span></span>

<span data-ttu-id="06785-628">다음 예제에서 `MapWhen`은 `api/HealthCheck` 엔드포인트에 대한 GET 요청을 받는 경우 상태 검사 미들웨어를 활성화하도록 요청 파이프라인을 분기합니다.</span><span class="sxs-lookup"><span data-stu-id="06785-628">In the following example, `MapWhen` branches the request pipeline to activate Health Checks Middleware if a GET request is received for the `api/HealthCheck` endpoint:</span></span>

```csharp
app.MapWhen(
    context => context.Request.Method == HttpMethod.Get.Method && 
        context.Request.Path.StartsWith("/api/HealthCheck"),
    builder => builder.UseHealthChecks());

app.UseMvc();
```

<span data-ttu-id="06785-629">자세한 내용은 <xref:fundamentals/middleware/index#use-run-and-map>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="06785-629">For more information, see <xref:fundamentals/middleware/index#use-run-and-map>.</span></span>

::: moniker-end
