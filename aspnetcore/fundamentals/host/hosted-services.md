---
title: ASP.NET Core에서 호스팅되는 서비스를 사용하는 백그라운드 작업
author: guardrex
description: ASP.NET Core에서 호스팅되는 서비스를 사용하는 백그라운드 작업을 구현하는 방법을 배웁니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/03/2019
uid: fundamentals/host/hosted-services
ms.openlocfilehash: 1db3ee1a9bcc0d41edf24df55bcd8d54fb0e9724
ms.sourcegitcommit: 215954a638d24124f791024c66fd4fb9109fd380
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/18/2019
ms.locfileid: "71081775"
---
# <a name="background-tasks-with-hosted-services-in-aspnet-core"></a><span data-ttu-id="a7a4e-103">ASP.NET Core에서 호스팅되는 서비스를 사용하는 백그라운드 작업</span><span class="sxs-lookup"><span data-stu-id="a7a4e-103">Background tasks with hosted services in ASP.NET Core</span></span>

<span data-ttu-id="a7a4e-104">작성자: [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="a7a4e-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="a7a4e-105">ASP.NET Core에서 백그라운드 작업은 *호스팅되는 서비스*로 구현될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a7a4e-105">In ASP.NET Core, background tasks can be implemented as *hosted services*.</span></span> <span data-ttu-id="a7a4e-106">호스티드 서비스는 <xref:Microsoft.Extensions.Hosting.IHostedService> 인터페이스를 구현하는 백그라운드 작업 논리가 있는 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="a7a4e-106">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="a7a4e-107">이 토픽에서는 세 가지 호스팅되는 서비스 예제를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="a7a4e-107">This topic provides three hosted service examples:</span></span>

* <span data-ttu-id="a7a4e-108">타이머에서 실행되는 백그라운드 작업.</span><span class="sxs-lookup"><span data-stu-id="a7a4e-108">Background task that runs on a timer.</span></span>
* <span data-ttu-id="a7a4e-109">[범위가 지정된 서비스](xref:fundamentals/dependency-injection#service-lifetimes)를 활성화하는 호스팅되는 서비스.</span><span class="sxs-lookup"><span data-stu-id="a7a4e-109">Hosted service that activates a [scoped service](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="a7a4e-110">범위가 지정된 서비스는 종속성 주입을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a7a4e-110">The scoped service can use dependency injection.</span></span>
* <span data-ttu-id="a7a4e-111">순차적으로 실행되는 대기 중인 백그라운드 작업.</span><span class="sxs-lookup"><span data-stu-id="a7a4e-111">Queued background tasks that run sequentially.</span></span>

<span data-ttu-id="a7a4e-112">[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="a7a4e-112">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="a7a4e-113">샘플 앱은 두 가지 버전으로 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="a7a4e-113">The sample app is provided in two versions:</span></span>

* <span data-ttu-id="a7a4e-114">웹 호스트 &ndash; 웹 호스트는 웹앱을 호스팅하는 데 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="a7a4e-114">Web Host &ndash; Web Host is useful for hosting web apps.</span></span> <span data-ttu-id="a7a4e-115">이 항목에 표시된 예제 코드는 웹 호스트 버전의 샘플에서 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="a7a4e-115">The example code shown in this topic is from Web Host version of the sample.</span></span> <span data-ttu-id="a7a4e-116">자세한 내용은 [웹 호스트](xref:fundamentals/host/web-host) 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a7a4e-116">For more information, see the [Web Host](xref:fundamentals/host/web-host) topic.</span></span>
* <span data-ttu-id="a7a4e-117">일반 호스트 &ndash; 일반 호스트는 ASP.NET Core 2.1의 새로운 기능입니다.</span><span class="sxs-lookup"><span data-stu-id="a7a4e-117">Generic Host &ndash; Generic Host is new in ASP.NET Core 2.1.</span></span> <span data-ttu-id="a7a4e-118">자세한 내용은 [일반 호스트](xref:fundamentals/host/generic-host) 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a7a4e-118">For more information, see the [Generic Host](xref:fundamentals/host/generic-host) topic.</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="worker-service-template"></a><span data-ttu-id="a7a4e-119">Worker Service 템플릿</span><span class="sxs-lookup"><span data-stu-id="a7a4e-119">Worker Service template</span></span>

<span data-ttu-id="a7a4e-120">ASP.NET Core Worker Service 템플릿은 장기간 실행되는 서비스 앱을 작성하기 위한 시작점을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="a7a4e-120">The ASP.NET Core Worker Service template provides a starting point for writing long running service apps.</span></span> <span data-ttu-id="a7a4e-121">템플릿을 호스팅되는 서비스 앱의 기반으로 사용하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="a7a4e-121">To use the template as a basis for a hosted services app:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="a7a4e-122">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a7a4e-122">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="a7a4e-123">새 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="a7a4e-123">Create a new project.</span></span>
1. <span data-ttu-id="a7a4e-124">**새 ASP.NET Core 웹 애플리케이션**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a7a4e-124">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="a7a4e-125">**새로 만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a7a4e-125">Select **Next**.</span></span>
1. <span data-ttu-id="a7a4e-126">**프로젝트 이름** 필드에 프로젝트 이름을 제공하거나 기본 프로젝트 이름을 수락합니다.</span><span class="sxs-lookup"><span data-stu-id="a7a4e-126">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="a7a4e-127">**만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a7a4e-127">Select **Create**.</span></span>
1. <span data-ttu-id="a7a4e-128">**새 ASP.NET Core 웹 애플리케이션 만들기** 대화 상자에서 **.NET Core** 및 **ASP.NET Core 3.0**이 선택되었는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="a7a4e-128">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.0** are selected.</span></span>
1. <span data-ttu-id="a7a4e-129">**Worker Service** 템플릿을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a7a4e-129">Select the **Worker Service** template.</span></span> <span data-ttu-id="a7a4e-130">**만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a7a4e-130">Select **Create**.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="a7a4e-131">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="a7a4e-131">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="a7a4e-132">명령 셸에서 [dotnet new](/dotnet/core/tools/dotnet-new) 명령과 함께 Worker Service(`worker`) 템플릿을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="a7a4e-132">Use the Worker Service (`worker`) template with the [dotnet new](/dotnet/core/tools/dotnet-new) command from a command shell.</span></span> <span data-ttu-id="a7a4e-133">다음 예제에서는 `ContosoWorkerService`라는 Worker Service 앱을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="a7a4e-133">In the following example, a Worker Service app is created named `ContosoWorkerService`.</span></span> <span data-ttu-id="a7a4e-134">명령이 실행될 때 `ContosoWorkerService` 앱의 폴더가 자동으로 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="a7a4e-134">A folder for the `ContosoWorkerService` app is created automatically when the command is executed.</span></span>

```dotnetcli
dotnet new worker -o ContosoWorkerService
```

---

::: moniker-end

## <a name="package"></a><span data-ttu-id="a7a4e-135">패키지</span><span class="sxs-lookup"><span data-stu-id="a7a4e-135">Package</span></span>

<span data-ttu-id="a7a4e-136">[Microsoft.AspNetCore.App 메타패키지](xref:fundamentals/metapackage-app)를 참조하거나 [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) 패키지에 대한 패키지 참조를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="a7a4e-136">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) package.</span></span>

## <a name="ihostedservice-interface"></a><span data-ttu-id="a7a4e-137">IHostedService 인터페이스</span><span class="sxs-lookup"><span data-stu-id="a7a4e-137">IHostedService interface</span></span>

<span data-ttu-id="a7a4e-138">호스팅되는 서비스는 <xref:Microsoft.Extensions.Hosting.IHostedService> 인터페이스를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="a7a4e-138">Hosted services implement the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="a7a4e-139">인터페이스는 호스트에 의해 관리되는 개체에 대한 두 가지 메서드를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="a7a4e-139">The interface defines two methods for objects that are managed by the host:</span></span>

* <span data-ttu-id="a7a4e-140">[StartAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*)&ndash;`StartAsync`에는 백그라운드 작업을 시작하는 논리가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="a7a4e-140">[StartAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*) &ndash; `StartAsync` contains the logic to start the background task.</span></span> <span data-ttu-id="a7a4e-141">[웹 호스트](xref:fundamentals/host/web-host)를 사용하는 경우 `StartAsync`는 서버가 시작되고 [IApplicationLifetime.ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*)가 트리거된 후에 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="a7a4e-141">When using the [Web Host](xref:fundamentals/host/web-host), `StartAsync` is called after the server has started and [IApplicationLifetime.ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) is triggered.</span></span> <span data-ttu-id="a7a4e-142">[제네릭 호스트](xref:fundamentals/host/generic-host)를 사용하는 경우 `ApplicationStarted`가 트리거되기 전에 `StartAsync`가 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="a7a4e-142">When using the [Generic Host](xref:fundamentals/host/generic-host), `StartAsync` is called before `ApplicationStarted` is triggered.</span></span>

* <span data-ttu-id="a7a4e-143">[StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) &ndash; 호스트가 정상적으로 종료될 때 트리거됩니다.</span><span class="sxs-lookup"><span data-stu-id="a7a4e-143">[StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) &ndash; Triggered when the host is performing a graceful shutdown.</span></span> <span data-ttu-id="a7a4e-144">`StopAsync`에는 백그라운드 작업을 종료하는 논리가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="a7a4e-144">`StopAsync` contains the logic to end the background task.</span></span> <span data-ttu-id="a7a4e-145">관리되지 않는 리소스를 삭제하려면 <xref:System.IDisposable> 및 [종료자(소멸자)](/dotnet/csharp/programming-guide/classes-and-structs/destructors)를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="a7a4e-145">Implement <xref:System.IDisposable> and [finalizers (destructors)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) to dispose of any unmanaged resources.</span></span>

  <span data-ttu-id="a7a4e-146">취소 토큰에는 종료 프로세스가 더 이상 정상화되지 않아야 함을 나타내는 기본 5초 시간 제한이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a7a4e-146">The cancellation token has a default five second timeout to indicate that the shutdown process should no longer be graceful.</span></span> <span data-ttu-id="a7a4e-147">토큰에 취소가 요청된 경우:</span><span class="sxs-lookup"><span data-stu-id="a7a4e-147">When cancellation is requested on the token:</span></span>

  * <span data-ttu-id="a7a4e-148">앱이 수행하는 나머지 백그라운드 작업은 중단해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a7a4e-148">Any remaining background operations that the app is performing should be aborted.</span></span>
  * <span data-ttu-id="a7a4e-149">`StopAsync`에 호출된 모든 메서드는 즉시 반환해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a7a4e-149">Any methods called in `StopAsync` should return promptly.</span></span>

  <span data-ttu-id="a7a4e-150">그러나 취소가 요청된 후 &mdash; 호출자가 모든 작업을 완료될 때까지 작업을 취소하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a7a4e-150">However, tasks aren't abandoned after cancellation is requested&mdash;the caller awaits all tasks to complete.</span></span>

  <span data-ttu-id="a7a4e-151">앱이 예기치 않게 종료된 경우(예: 앱의 프로세스가 실패한 경우), `StopAsync`가 호출되지 않을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a7a4e-151">If the app shuts down unexpectedly (for example, the app's process fails), `StopAsync` might not be called.</span></span> <span data-ttu-id="a7a4e-152">따라서 메서드 호출이나 `StopAsync`에서 수행된 작업이 발생하지 않을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a7a4e-152">Therefore, any methods called or operations conducted in `StopAsync` might not occur.</span></span>

  <span data-ttu-id="a7a4e-153">기본 5초 시스템 종료 시간 제한을 연장하려면 다음을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="a7a4e-153">To extend the default five second shutdown timeout, set:</span></span>

  * <span data-ttu-id="a7a4e-154"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> 일반 호스트를 사용하는 경우</span><span class="sxs-lookup"><span data-stu-id="a7a4e-154"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> when using Generic Host.</span></span> <span data-ttu-id="a7a4e-155">자세한 내용은 <xref:fundamentals/host/generic-host#shutdown-timeout>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a7a4e-155">For more information, see <xref:fundamentals/host/generic-host#shutdown-timeout>.</span></span>
  * <span data-ttu-id="a7a4e-156">웹 호스트를 사용하는 경우 시스템 종료 시간 제한 호스트 구성 설정.</span><span class="sxs-lookup"><span data-stu-id="a7a4e-156">Shutdown timeout host configuration setting when using Web Host.</span></span> <span data-ttu-id="a7a4e-157">자세한 내용은 <xref:fundamentals/host/web-host#shutdown-timeout>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a7a4e-157">For more information, see <xref:fundamentals/host/web-host#shutdown-timeout>.</span></span>

<span data-ttu-id="a7a4e-158">호스팅되는 서비스는 앱 시작 시 한 번 활성화되고 앱 종료 시 정상적으로 종료됩니다.</span><span class="sxs-lookup"><span data-stu-id="a7a4e-158">The hosted service is activated once at app startup and gracefully shut down at app shutdown.</span></span> <span data-ttu-id="a7a4e-159">백그라운드 작업을 실행하는 동안 오류가 발생하면 `StopAsync`가 호출되지 않아도 `Dispose`를 호출해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a7a4e-159">If an error is thrown during background task execution, `Dispose` should be called even if `StopAsync` isn't called.</span></span>

## <a name="timed-background-tasks"></a><span data-ttu-id="a7a4e-160">시간이 지정된 백그라운드 작업</span><span class="sxs-lookup"><span data-stu-id="a7a4e-160">Timed background tasks</span></span>

<span data-ttu-id="a7a4e-161">시간이 지정된 백그라운드 작업은 [System.Threading.Timer](xref:System.Threading.Timer) 클래스를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="a7a4e-161">A timed background task makes use of the [System.Threading.Timer](xref:System.Threading.Timer) class.</span></span> <span data-ttu-id="a7a4e-162">타이머가 작업의 `DoWork` 메서드를 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="a7a4e-162">The timer triggers the task's `DoWork` method.</span></span> <span data-ttu-id="a7a4e-163">서비스 컨테이너가 `Dispose`에서 삭제될 때 `StopAsync`에서 타이머가 비활성화되고 삭제됩니다.</span><span class="sxs-lookup"><span data-stu-id="a7a4e-163">The timer is disabled on `StopAsync` and disposed when the service container is disposed on `Dispose`:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample-WebHost/Services/TimedHostedService.cs?name=snippet1&highlight=15-16,30,37)]

<span data-ttu-id="a7a4e-164">서비스는 `AddHostedService` 확장 메서드를 사용하여 `Startup.ConfigureServices`에 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="a7a4e-164">The service is registered in `Startup.ConfigureServices` with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample-WebHost/Startup.cs?name=snippet1)]

## <a name="consuming-a-scoped-service-in-a-background-task"></a><span data-ttu-id="a7a4e-165">백그라운드 작업에서 범위가 지정된 서비스 사용</span><span class="sxs-lookup"><span data-stu-id="a7a4e-165">Consuming a scoped service in a background task</span></span>

<span data-ttu-id="a7a4e-166">`IHostedService` 내에서 [범위가 지정된 서비스](xref:fundamentals/dependency-injection#service-lifetimes)를 사용하려면 범위를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="a7a4e-166">To use [scoped services](xref:fundamentals/dependency-injection#service-lifetimes) within an `IHostedService`, create a scope.</span></span> <span data-ttu-id="a7a4e-167">기본적으로 호스팅되는 서비스에 대한 범위는 생성되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a7a4e-167">No scope is created for a hosted service by default.</span></span>

<span data-ttu-id="a7a4e-168">범위가 지정된 백그라운드 작업 서비스에는 백그라운드 작업의 논리가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="a7a4e-168">The scoped background task service contains the background task's logic.</span></span> <span data-ttu-id="a7a4e-169">다음 예제에서는 <xref:Microsoft.Extensions.Logging.ILogger>가 서비스에 삽입됩니다.</span><span class="sxs-lookup"><span data-stu-id="a7a4e-169">In the following example, an <xref:Microsoft.Extensions.Logging.ILogger> is injected into the service:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample-WebHost/Services/ScopedProcessingService.cs?name=snippet1)]

<span data-ttu-id="a7a4e-170">호스팅되는 서비스는 범위가 지정된 백그라운드 작업 서비스를 해결하여 `DoWork` 메서드를 호출하는 범위를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="a7a4e-170">The hosted service creates a scope to resolve the scoped background task service to call its `DoWork` method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample-WebHost/Services/ConsumeScopedServiceHostedService.cs?name=snippet1&highlight=29-36)]

<span data-ttu-id="a7a4e-171">서비스는 `Startup.ConfigureServices`에 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="a7a4e-171">The services are registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="a7a4e-172">`IHostedService` 구현은 `AddHostedService` 확장 메서드를 사용하여 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="a7a4e-172">The `IHostedService` implementation is registered with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample-WebHost/Startup.cs?name=snippet2)]

## <a name="queued-background-tasks"></a><span data-ttu-id="a7a4e-173">대기 중인 백그라운드 작업</span><span class="sxs-lookup"><span data-stu-id="a7a4e-173">Queued background tasks</span></span>

<span data-ttu-id="a7a4e-174">백그라운드 작업 큐는 .NET 4.x <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*>([ASP.NET Core 3.0용으로 기본 제공될 예정](https://github.com/aspnet/Hosting/issues/1280))을 기반으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="a7a4e-174">A background task queue is based on the .NET 4.x <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> ([tentatively scheduled to be built-in for ASP.NET Core 3.0](https://github.com/aspnet/Hosting/issues/1280)):</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample-WebHost/Services/BackgroundTaskQueue.cs?name=snippet1)]

<span data-ttu-id="a7a4e-175">`QueueHostedService`에서 큐의 백그라운드 작업은 큐에서 제거되고 장기 실행 `IHostedService`를 구현하기 위한 기본 클래스인 <xref:Microsoft.Extensions.Hosting.BackgroundService>로 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="a7a4e-175">In `QueueHostedService`, background tasks in the queue are dequeued and executed as a <xref:Microsoft.Extensions.Hosting.BackgroundService>, which is a base class for implementing a long running `IHostedService`:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample-WebHost/Services/QueuedHostedService.cs?name=snippet1&highlight=21,25)]

<span data-ttu-id="a7a4e-176">서비스는 `Startup.ConfigureServices`에 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="a7a4e-176">The services are registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="a7a4e-177">`IHostedService` 구현은 `AddHostedService` 확장 메서드를 사용하여 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="a7a4e-177">The `IHostedService` implementation is registered with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample-WebHost/Startup.cs?name=snippet3)]

<span data-ttu-id="a7a4e-178">인덱스 페이지 모델 클래스에서:</span><span class="sxs-lookup"><span data-stu-id="a7a4e-178">In the Index page model class:</span></span>

* <span data-ttu-id="a7a4e-179">`IBackgroundTaskQueue`는 생성자에 삽입되고 `Queue`에 할당됩니다.</span><span class="sxs-lookup"><span data-stu-id="a7a4e-179">The `IBackgroundTaskQueue` is injected into the constructor and assigned to `Queue`.</span></span>
* <span data-ttu-id="a7a4e-180"><xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory>가 삽입되어 `_serviceScopeFactory`에 할당됩니다.</span><span class="sxs-lookup"><span data-stu-id="a7a4e-180">An <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory> is injected and assigned to `_serviceScopeFactory`.</span></span> <span data-ttu-id="a7a4e-181">팩터리는 범위 내에서 서비스를 만드는 데 사용되는 <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>의 인스턴스를 만드는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="a7a4e-181">The factory is used to create instances of <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>, which is used to create services within a scope.</span></span> <span data-ttu-id="a7a4e-182">앱의 `AppDbContext`([범위가 지정된 서비스](xref:fundamentals/dependency-injection#service-lifetimes))를 사용하여 `IBackgroundTaskQueue`(싱글톤 서비스)에 데이터베이스 레코드를 작성하는 범위가 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="a7a4e-182">A scope is created in order to use the app's `AppDbContext` (a [scoped service](xref:fundamentals/dependency-injection#service-lifetimes)) to write database records in the `IBackgroundTaskQueue` (a singleton service).</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample-WebHost/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="a7a4e-183">인덱스 페이지에서 **작업 추가** 단추를 선택하면 `OnPostAddTask` 메서드가 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="a7a4e-183">When the **Add Task** button is selected on the Index page, the `OnPostAddTask` method is executed.</span></span> <span data-ttu-id="a7a4e-184">`QueueBackgroundWorkItem`이 호출되어 작업 항목을 큐에 넣습니다.</span><span class="sxs-lookup"><span data-stu-id="a7a4e-184">`QueueBackgroundWorkItem` is called to enqueue the work item:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample-WebHost/Pages/Index.cshtml.cs?name=snippet2)]

## <a name="additional-resources"></a><span data-ttu-id="a7a4e-185">추가 자료</span><span class="sxs-lookup"><span data-stu-id="a7a4e-185">Additional resources</span></span>

* [<span data-ttu-id="a7a4e-186">IHostedService 및 BackgroundService 클래스를 사용하여 마이크로 서비스에서 백그라운드 작업 구현</span><span class="sxs-lookup"><span data-stu-id="a7a4e-186">Implement background tasks in microservices with IHostedService and the BackgroundService class</span></span>](/dotnet/standard/microservices-architecture/multi-container-microservice-net-applications/background-tasks-with-ihostedservice)
* <xref:System.Threading.Timer>
