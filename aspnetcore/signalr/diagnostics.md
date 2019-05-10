---
title: 로깅 및 ASP.NET Core SignalR의 진단
author: anurse
description: ASP.NET Core SignalR 앱에서 진단 수집 하는 방법에 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: anurse
ms.custom: signalr
ms.date: 02/27/2019
uid: signalr/diagnostics
ms.openlocfilehash: b6bd21314ed183488999bcff3553e53493537a11
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64896890"
---
# <a name="logging-and-diagnostics-in-aspnet-core-signalr"></a><span data-ttu-id="946e4-103">로깅 및 ASP.NET Core SignalR의 진단</span><span class="sxs-lookup"><span data-stu-id="946e4-103">Logging and diagnostics in ASP.NET Core SignalR</span></span>

<span data-ttu-id="946e4-104">작성자: [Andrew Stanton-Nurse](https://twitter.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="946e4-104">By [Andrew Stanton-Nurse](https://twitter.com/anurse)</span></span>

<span data-ttu-id="946e4-105">이 문서에서는 문제를 해결 하는 ASP.NET Core SignalR 앱에서 진단 수집에 대 한 지침을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="946e4-105">This article provides guidance for gathering diagnostics from your ASP.NET Core SignalR app to help troubleshoot issues.</span></span>

## <a name="server-side-logging"></a><span data-ttu-id="946e4-106">서버 쪽 로깅</span><span class="sxs-lookup"><span data-stu-id="946e4-106">Server-side logging</span></span>

> [!WARNING]
> <span data-ttu-id="946e4-107">서버 쪽 로그는 앱에서 중요 한 정보를 포함할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="946e4-107">Server-side logs may contain sensitive information from your app.</span></span> <span data-ttu-id="946e4-108">**되지** 프로덕션 앱에서 GitHub와 같은 공개 포럼에 원시 로그를 게시 합니다.</span><span class="sxs-lookup"><span data-stu-id="946e4-108">**Never** post raw logs from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="946e4-109">SignalR ASP.NET Core의 일부 이므로 ASP.NET Core 로깅 시스템을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="946e4-109">Since SignalR is part of ASP.NET Core, it uses the ASP.NET Core logging system.</span></span> <span data-ttu-id="946e4-110">기본 구성으로 아주 적은 양의 정보를 기록 하는 SignalR 하지만 구성할 수이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="946e4-110">In the default configuration, SignalR logs very little information, but this can configured.</span></span> <span data-ttu-id="946e4-111">설명서를 참조 [ASP.NET Core 로깅](xref:fundamentals/logging/index#configuration) ASP.NET Core 로깅 구성에 대 한 세부 정보에 대 한 합니다.</span><span class="sxs-lookup"><span data-stu-id="946e4-111">See the documentation on [ASP.NET Core logging](xref:fundamentals/logging/index#configuration) for details on configuring ASP.NET Core logging.</span></span>

<span data-ttu-id="946e4-112">SignalR 두 개의 거 범주를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="946e4-112">SignalR uses two logger categories:</span></span>

* <span data-ttu-id="946e4-113">`Microsoft.AspNetCore.SignalR` -허브 프로토콜과 관련 된 로그에 대 한 허브를 활성화, 메서드 및 기타 허브 관련 작업을 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="946e4-113">`Microsoft.AspNetCore.SignalR` - for logs related to Hub Protocols, activating Hubs, invoking methods, and other Hub-related activities.</span></span>
* <span data-ttu-id="946e4-114">`Microsoft.AspNetCore.Http.Connections` -Websocket, 긴 폴링 및 Server-Sent 이벤트 및 SignalR 늘리고 저수준 인프라와 같은 전송와 관련 된 로그에 대 한 합니다.</span><span class="sxs-lookup"><span data-stu-id="946e4-114">`Microsoft.AspNetCore.Http.Connections` - for logs related to transports such as WebSockets, Long Polling and Server-Sent Events and low-level SignalR infrastructure.</span></span>

<span data-ttu-id="946e4-115">SignalR에서 자세한 로그를 활성화 하려면 앞에 접두사에 대 모두를 구성 합니다 `Debug` 수준에 `appsettings.json` 파일에 다음 항목을 추가 하 여는 `LogLevel` 하위 섹션 `Logging`:</span><span class="sxs-lookup"><span data-stu-id="946e4-115">To enable detailed logs from SignalR, configure both of the preceding prefixes to the `Debug` level in your `appsettings.json` file by adding the following items to the `LogLevel` sub-section in `Logging`:</span></span>

[!code-json[Configuring logging](diagnostics/logging-config.json?highlight=7-8)]

<span data-ttu-id="946e4-116">코드에도 구성할 수 있습니다 프로그램 `CreateWebHostBuilder` 메서드:</span><span class="sxs-lookup"><span data-stu-id="946e4-116">You can also configure this in code in your `CreateWebHostBuilder` method:</span></span>

[!code-csharp[Configuring logging in code](diagnostics/logging-config-code.cs?highlight=5-6)]

<span data-ttu-id="946e4-117">JSON 기반 구성을 사용 하지 않는 경우 구성 시스템에서 다음 구성 값을 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="946e4-117">If you aren't using JSON-based configuration, set the following configuration values in your configuration system:</span></span>

* `Logging:LogLevel:Microsoft.AspNetCore.SignalR` = `Debug`
* `Logging:LogLevel:Microsoft.AspNetCore.Http.Connections` = `Debug`

<span data-ttu-id="946e4-118">중첩 된 구성 값을 지정 하는 방법을 결정 구성 시스템에 대 한 설명서를 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="946e4-118">Check the documentation for your configuration system to determine how to specify nested configuration values.</span></span> <span data-ttu-id="946e4-119">예를 들어, 환경 변수를 사용 하는 경우 두 `_` 문자를 대신 사용 합니다 `:` (같은: `Logging__LogLevel__Microsoft.AspNetCore.SignalR`).</span><span class="sxs-lookup"><span data-stu-id="946e4-119">For example, when using environment variables, two `_` characters are used instead of the `:` (such as: `Logging__LogLevel__Microsoft.AspNetCore.SignalR`).</span></span>

<span data-ttu-id="946e4-120">사용 하는 것이 좋습니다는 `Debug` 자세한 앱에 대 한 진단을 수집할 때 수준입니다.</span><span class="sxs-lookup"><span data-stu-id="946e4-120">We recommend using the `Debug` level when gathering more detailed diagnostics for your app.</span></span> <span data-ttu-id="946e4-121">`Trace` 수준 매우 낮은 수준의 진단 생성 및 앱의 문제를 진단 하는 데 필요한 경우는 거의 없습니다.</span><span class="sxs-lookup"><span data-stu-id="946e4-121">The `Trace` level produces very low-level diagnostics and is rarely needed to diagnose issues in your app.</span></span>

## <a name="access-server-side-logs"></a><span data-ttu-id="946e4-122">서버 쪽 로그 액세스</span><span class="sxs-lookup"><span data-stu-id="946e4-122">Access server-side logs</span></span>

<span data-ttu-id="946e4-123">서버 쪽 로그에 액세스 하는 방법을 실행 하는 환경에 따라 달라 집니다.</span><span class="sxs-lookup"><span data-stu-id="946e4-123">How you access server-side logs depends on the environment in which you're running.</span></span>

### <a name="as-a-console-app-outside-iis"></a><span data-ttu-id="946e4-124">IIS 외부 콘솔 앱</span><span class="sxs-lookup"><span data-stu-id="946e4-124">As a console app outside IIS</span></span>

<span data-ttu-id="946e4-125">콘솔 앱에서 실행 하는 경우는 [콘솔으로 거](xref:fundamentals/logging/index#console-provider) 기본적으로 사용 하도록 설정 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="946e4-125">If you're running in a console app, the [Console logger](xref:fundamentals/logging/index#console-provider) should be enabled by default.</span></span> <span data-ttu-id="946e4-126">SignalR 로그는 콘솔에 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="946e4-126">SignalR logs will appear in the console.</span></span>

### <a name="within-iis-express-from-visual-studio"></a><span data-ttu-id="946e4-127">Visual Studio에서 IIS Express에서</span><span class="sxs-lookup"><span data-stu-id="946e4-127">Within IIS Express from Visual Studio</span></span>

<span data-ttu-id="946e4-128">Visual Studio에서 로그 출력을 표시 합니다 **출력** 창입니다.</span><span class="sxs-lookup"><span data-stu-id="946e4-128">Visual Studio displays the log output in the **Output** window.</span></span> <span data-ttu-id="946e4-129">선택 된 **ASP.NET Core 웹 서버** 옵션 드롭다운 합니다.</span><span class="sxs-lookup"><span data-stu-id="946e4-129">Select the **ASP.NET Core Web Server** drop down option.</span></span>

### <a name="azure-app-service"></a><span data-ttu-id="946e4-130">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="946e4-130">Azure App Service</span></span>

<span data-ttu-id="946e4-131">Azure App Service 포털의 "진단 로그" 섹션의 "응용 프로그램 로깅 (파일 시스템)" 옵션을 설정 하 고 수준을 구성 `Verbose`합니다.</span><span class="sxs-lookup"><span data-stu-id="946e4-131">Enable the "Application Logging (Filesystem)" option in the "Diagnostics logs" section of the Azure App Service portal and configure the Level to `Verbose`.</span></span> <span data-ttu-id="946e4-132">로그 로그 App Service의 파일 시스템에서와 같이 "로그 스트리밍" 서비스에도 사용할 수 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="946e4-132">Logs should be available from the "Log streaming" service, as well as in logs on the file system of your App Service.</span></span> <span data-ttu-id="946e4-133">자세한 내용은 설명서를 참조 하세요 [Azure 로그 스트리밍을](xref:fundamentals/logging/index#azure-log-streaming)합니다.</span><span class="sxs-lookup"><span data-stu-id="946e4-133">For more information, see the documentation on [Azure log streaming](xref:fundamentals/logging/index#azure-log-streaming).</span></span>

### <a name="other-environments"></a><span data-ttu-id="946e4-134">다른 환경</span><span class="sxs-lookup"><span data-stu-id="946e4-134">Other environments</span></span>

<span data-ttu-id="946e4-135">다른 실행 중인 경우 (Docker, Kubernetes, Windows 서비스, 등) 환경에서 전체 설명서를 참조 [ASP.NET Core 로깅](xref:fundamentals/logging/index) 환경에 적합 한 로깅 공급자를 구성 하는 방법에 대 한 자세한 내용은 합니다.</span><span class="sxs-lookup"><span data-stu-id="946e4-135">If you're running in another environment (Docker, Kubernetes, Windows Service, etc.), see the full documentation on [ASP.NET Core Logging](xref:fundamentals/logging/index) for more information on how to configure logging providers suitable to your environment.</span></span>

## <a name="javascript-client-logging"></a><span data-ttu-id="946e4-136">JavaScript 클라이언트 로그</span><span class="sxs-lookup"><span data-stu-id="946e4-136">JavaScript client logging</span></span>

> [!WARNING]
> <span data-ttu-id="946e4-137">클라이언트 쪽 로그는 앱에서 중요 한 정보를 포함할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="946e4-137">Client-side logs may contain sensitive information from your app.</span></span> <span data-ttu-id="946e4-138">**되지** 프로덕션 앱에서 GitHub와 같은 공개 포럼에 원시 로그를 게시 합니다.</span><span class="sxs-lookup"><span data-stu-id="946e4-138">**Never** post raw logs from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="946e4-139">JavaScript 클라이언트를 사용 하는 경우에 사용 하 여 로깅 옵션을 구성할 수 있습니다 합니다 `configureLogging` 메서드를 `HubConnectionBuilder`:</span><span class="sxs-lookup"><span data-stu-id="946e4-139">When using the JavaScript client, you can configure logging options using the `configureLogging` method on `HubConnectionBuilder`:</span></span>

[!code-javascript[Configuring logging in the JavaScript client](diagnostics/logging-config-js.js?highlight=3)]

<span data-ttu-id="946e4-140">로깅을 완전히 비활성화시키려면 `configureLogging` 메서드에서 `signalR.LogLevel.None`을 지정하십시오.</span><span class="sxs-lookup"><span data-stu-id="946e4-140">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="946e4-141">다음 표에서 JavaScript 클라이언트에 사용할 로그 수준을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="946e4-141">The following table shows log levels available to the JavaScript client.</span></span> <span data-ttu-id="946e4-142">로그 수준은 다음이 값 중 하나로 설정 로깅을 수준 및 테이블에서 위의 모든 수준이 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="946e4-142">Setting the log level to one of these values enables logging at that level and all levels above it in the table.</span></span>

| <span data-ttu-id="946e4-143">수준</span><span class="sxs-lookup"><span data-stu-id="946e4-143">Level</span></span> | <span data-ttu-id="946e4-144">설명</span><span class="sxs-lookup"><span data-stu-id="946e4-144">Description</span></span> |
| ----- | ----------- |
| `None` | <span data-ttu-id="946e4-145">메시지가 기록되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="946e4-145">No messages are logged.</span></span> |
| `Critical` | <span data-ttu-id="946e4-146">전체 앱에서 발생한 오류를 나타내는 메시지입니다.</span><span class="sxs-lookup"><span data-stu-id="946e4-146">Messages that indicate a failure in the entire app.</span></span> |
| `Error` | <span data-ttu-id="946e4-147">현재 작업에서 발생한 오류를 나타내는 메시지입니다.</span><span class="sxs-lookup"><span data-stu-id="946e4-147">Messages that indicate a failure in the current operation.</span></span> |
| `Warning` | <span data-ttu-id="946e4-148">치명적이지 않은 문제를 나타내는 메시지입니다.</span><span class="sxs-lookup"><span data-stu-id="946e4-148">Messages that indicate a non-fatal problem.</span></span> |
| `Information` | <span data-ttu-id="946e4-149">정보 메시지입니다.</span><span class="sxs-lookup"><span data-stu-id="946e4-149">Informational messages.</span></span> |
| `Debug` | <span data-ttu-id="946e4-150">디버깅에 유용한 진단 메시지입니다.</span><span class="sxs-lookup"><span data-stu-id="946e4-150">Diagnostic messages useful for debugging.</span></span> |
| `Trace` | <span data-ttu-id="946e4-151">특정 문제를 진단하기 위해 의도된 매우 자세한 진단 메시지입니다.</span><span class="sxs-lookup"><span data-stu-id="946e4-151">Very detailed diagnostic messages designed for diagnosing specific issues.</span></span> |

<span data-ttu-id="946e4-152">자세한 정도 구성한 후 로그 브라우저 콘솔 (또는 NodeJS 앱에서 표준 출력)에 기록 됩니다.</span><span class="sxs-lookup"><span data-stu-id="946e4-152">Once you've configured the verbosity, the logs will be written to the Browser Console (or Standard Output in a NodeJS app).</span></span>

<span data-ttu-id="946e4-153">사용자 지정 로깅 시스템으로 로그를 전송 하려는 경우 구현 하는 JavaScript 개체 제공할 수 있습니다는 `ILogger` 인터페이스입니다.</span><span class="sxs-lookup"><span data-stu-id="946e4-153">If you want to send logs to a custom logging system, you can provide a JavaScript object implementing the `ILogger` interface.</span></span> <span data-ttu-id="946e4-154">구현 해야 하는 유일한 방법은 `log`, 사용 하는 이벤트의 수준 및 메시지 이벤트와 연결 합니다.</span><span class="sxs-lookup"><span data-stu-id="946e4-154">The only method that needs to be implemented is `log`, which takes the level of the event and the message associated with the event.</span></span> <span data-ttu-id="946e4-155">예를 들어:</span><span class="sxs-lookup"><span data-stu-id="946e4-155">For example:</span></span>

[!code-typescript[Creating a custom logger](diagnostics/custom-logger.ts?highlight=3-7,13)]

## <a name="net-client-logging"></a><span data-ttu-id="946e4-156">.NET 클라이언트 로그</span><span class="sxs-lookup"><span data-stu-id="946e4-156">.NET client logging</span></span>

> [!WARNING]
> <span data-ttu-id="946e4-157">클라이언트 쪽 로그는 앱에서 중요 한 정보를 포함할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="946e4-157">Client-side logs may contain sensitive information from your app.</span></span> <span data-ttu-id="946e4-158">**되지** 프로덕션 앱에서 GitHub와 같은 공개 포럼에 원시 로그를 게시 합니다.</span><span class="sxs-lookup"><span data-stu-id="946e4-158">**Never** post raw logs from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="946e4-159">.NET 클라이언트에서 로그를 가져오려면 사용할 수 있습니다 합니다 `ConfigureLogging` 메서드를 `HubConnectionBuilder`입니다.</span><span class="sxs-lookup"><span data-stu-id="946e4-159">To get logs from the .NET client, you can use the `ConfigureLogging` method on `HubConnectionBuilder`.</span></span> <span data-ttu-id="946e4-160">동일한 방식으로 작동 합니다 `ConfigureLogging` 메서드를 `WebHostBuilder` 고 `HostBuilder`입니다.</span><span class="sxs-lookup"><span data-stu-id="946e4-160">This works the same way as the `ConfigureLogging` method on `WebHostBuilder` and `HostBuilder`.</span></span> <span data-ttu-id="946e4-161">ASP.NET Core에서 사용 하면 동일한 로깅 공급자를 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="946e4-161">You can configure the same logging providers you use in ASP.NET Core.</span></span> <span data-ttu-id="946e4-162">그러나 수동으로 설치 하 고 개별 로깅 공급자에 대 한 NuGet 패키지를 사용 하도록 설정 해야 할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="946e4-162">However, you have to manually install and enable the NuGet packages for the individual logging providers.</span></span>

### <a name="console-logging"></a><span data-ttu-id="946e4-163">콘솔 로깅</span><span class="sxs-lookup"><span data-stu-id="946e4-163">Console logging</span></span>

<span data-ttu-id="946e4-164">콘솔 로깅을 사용 하려면 추가 합니다 [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) 패키지 있습니다.</span><span class="sxs-lookup"><span data-stu-id="946e4-164">In order to enable Console logging, add the [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) package.</span></span> <span data-ttu-id="946e4-165">그런 다음 사용 된 `AddConsole` 콘솔으로 거를 구성 하는 방법:</span><span class="sxs-lookup"><span data-stu-id="946e4-165">Then, use the `AddConsole` method to configure the console logger:</span></span>

[!code-csharp[Configuring console logging in .NET client](diagnostics/net-client-console-log.cs?highlight=6)]

### <a name="debug-output-window-logging"></a><span data-ttu-id="946e4-166">디버그 출력 창 로깅</span><span class="sxs-lookup"><span data-stu-id="946e4-166">Debug output window logging</span></span>

<span data-ttu-id="946e4-167">로 로그를 구성할 수도 있습니다는 **출력** Visual Studio의 창.</span><span class="sxs-lookup"><span data-stu-id="946e4-167">You can also configure logs to go to the **Output** window in Visual Studio.</span></span> <span data-ttu-id="946e4-168">설치를 [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) 패키지 및 사용 된 `AddDebug` 메서드:</span><span class="sxs-lookup"><span data-stu-id="946e4-168">Install the [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) package and use the `AddDebug` method:</span></span>

[!code-csharp[Configuring debug output window logging in .NET client](diagnostics/net-client-debug-log.cs?highlight=6)]

### <a name="other-logging-providers"></a><span data-ttu-id="946e4-169">다른 로깅 공급자</span><span class="sxs-lookup"><span data-stu-id="946e4-169">Other logging providers</span></span>

<span data-ttu-id="946e4-170">SignalR Serilog, Seq, NLog 또는와 통합 되는 다른 로깅 시스템 같은 다른 로깅 공급자를 지 원하는 `Microsoft.Extensions.Logging`합니다.</span><span class="sxs-lookup"><span data-stu-id="946e4-170">SignalR supports other logging providers such as Serilog, Seq, NLog, or any other logging system that integrates with `Microsoft.Extensions.Logging`.</span></span> <span data-ttu-id="946e4-171">로깅 시스템에서 제공 하는 경우는 `ILoggerProvider`를 사용 하 여 등록할 수 있습니다 `AddProvider`:</span><span class="sxs-lookup"><span data-stu-id="946e4-171">If your logging system provides an `ILoggerProvider`, you can register it with `AddProvider`:</span></span>

[!code-csharp[Configuring a custom logging provider in .NET client](diagnostics/net-client-custom-log.cs?highlight=6)]

### <a name="control-verbosity"></a><span data-ttu-id="946e4-172">컨트롤의 자세한 정도</span><span class="sxs-lookup"><span data-stu-id="946e4-172">Control verbosity</span></span>

<span data-ttu-id="946e4-173">앱에서 다른 위치에서 기록 하는 경우 기본 수준을 변경 `Debug` 너무 자세한 정보 표시 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="946e4-173">If you are logging from other places in your app, changing the default level to `Debug` may be too verbose.</span></span> <span data-ttu-id="946e4-174">SignalR 로그에 대 한 로깅 수준을 구성 하는 필터를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="946e4-174">You can use a Filter to configure the logging level for SignalR logs.</span></span> <span data-ttu-id="946e4-175">이렇게 하려면 코드의 대부분 동일한 방식으로 서버에서:</span><span class="sxs-lookup"><span data-stu-id="946e4-175">This can be done in code, in much the same way as on the server:</span></span>

[!code-csharp[Controlling verbosity in .NET client](diagnostics/logging-config-client-code.cs?highlight=9-10)]

## <a name="network-traces"></a><span data-ttu-id="946e4-176">네트워크 추적</span><span class="sxs-lookup"><span data-stu-id="946e4-176">Network traces</span></span>

> [!WARNING]
> <span data-ttu-id="946e4-177">네트워크 추적에는 앱에서 보낸 모든 메시지의 전체 콘텐츠를 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="946e4-177">A network trace contains the full contents of every message sent by your app.</span></span> <span data-ttu-id="946e4-178">**되지** 원시 네트워크 추적을 프로덕션 앱에서 GitHub와 같은 공개 포럼에 게시 합니다.</span><span class="sxs-lookup"><span data-stu-id="946e4-178">**Never** post raw network traces from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="946e4-179">문제가 발생 하는 경우 네트워크 추적 유용한 정보가 많은 경우에 따라 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="946e4-179">If you encounter an issue, a network trace can sometimes provide a lot of helpful information.</span></span> <span data-ttu-id="946e4-180">문제 추적기에서 문제를 제출 하려는 경우 특히 유용 합니다.</span><span class="sxs-lookup"><span data-stu-id="946e4-180">This is particularly useful if you're going to file an issue on our issue tracker.</span></span>

## <a name="collect-a-network-trace-with-fiddler-preferred-option"></a><span data-ttu-id="946e4-181">(기본 옵션) Fiddler 사용 하 여 네트워크 추적을 수집 합니다.</span><span class="sxs-lookup"><span data-stu-id="946e4-181">Collect a network trace with Fiddler (preferred option)</span></span>

<span data-ttu-id="946e4-182">이 메서드는 모든 앱에 대해 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="946e4-182">This method works for all apps.</span></span>

<span data-ttu-id="946e4-183">Fiddler는 HTTP 추적을 수집 하기 위한 강력한 도구입니다.</span><span class="sxs-lookup"><span data-stu-id="946e4-183">Fiddler is a very powerful tool for collecting HTTP traces.</span></span> <span data-ttu-id="946e4-184">설치할 [telerik.com/fiddler](https://www.telerik.com/fiddler), 시작 하 고 다음 앱을 실행 하 고 문제를 재현 합니다.</span><span class="sxs-lookup"><span data-stu-id="946e4-184">Install it from [telerik.com/fiddler](https://www.telerik.com/fiddler), launch it, and then run your app and reproduce the issue.</span></span> <span data-ttu-id="946e4-185">Fiddler Windows를 사용할 수 있으며는 macOS 및 Linux에 대 한 베타 버전이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="946e4-185">Fiddler is available for Windows, and there are beta versions for macOS and Linux.</span></span>

<span data-ttu-id="946e4-186">HTTPS를 사용 하 여 연결 하는 경우 몇 가지 추가 단계 Fiddler HTTPS 트래픽 암호를 해독할 수 있도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="946e4-186">If you connect using HTTPS, there are some extra steps to ensure Fiddler can decrypt the HTTPS traffic.</span></span> <span data-ttu-id="946e4-187">자세한 내용은 참조는 [Fiddler 설명서](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS)합니다.</span><span class="sxs-lookup"><span data-stu-id="946e4-187">For more details, see the [Fiddler documentation](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS).</span></span>

<span data-ttu-id="946e4-188">추적을 수집한 후 선택 하 여 추적을 내보낼 수 있습니다 **파일** > **저장** > **모든 세션...**  메뉴 모음에서.</span><span class="sxs-lookup"><span data-stu-id="946e4-188">Once you've collected the trace, you can export the trace by choosing **File** > **Save** > **All Sessions...** from the menu bar.</span></span>

![Fiddler에서 모든 세션 내보내기](diagnostics/fiddler-export.png)

## <a name="collect-a-network-trace-with-tcpdump-macos-and-linux-only"></a><span data-ttu-id="946e4-190">Tcpdump (macOS 및 Linux에만 해당)를 사용 하 여 네트워크 추적을 수집 합니다.</span><span class="sxs-lookup"><span data-stu-id="946e4-190">Collect a network trace with tcpdump (macOS and Linux only)</span></span>

<span data-ttu-id="946e4-191">이 메서드는 모든 앱에 대해 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="946e4-191">This method works for all apps.</span></span>

<span data-ttu-id="946e4-192">Tcpdump 명령 셸에서 다음 명령을 실행 하 여 원시 TCP 추적을 수집할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="946e4-192">You can collect raw TCP traces using tcpdump by running the following command from a command shell.</span></span> <span data-ttu-id="946e4-193">해야 할 수 있습니다 `root` 사용 하 여 명령을 접두사 또는 `sudo` 권한 오류가 발생할 경우:</span><span class="sxs-lookup"><span data-stu-id="946e4-193">You may need to be `root` or prefix the command with `sudo` if you get a permissions error:</span></span>

```console
tcpdump -i [interface] -w trace.pcap
```

<span data-ttu-id="946e4-194">대체 `[interface]` 에서 캡처 하려는 네트워크 인터페이스를 사용 하 여 합니다.</span><span class="sxs-lookup"><span data-stu-id="946e4-194">Replace `[interface]` with the network interface you wish to capture on.</span></span> <span data-ttu-id="946e4-195">일반적으로이 같습니다 `/dev/eth0` (표준 이더넷 인터페이스)에 대 한 또는 `/dev/lo0` (localhost 트래픽용).</span><span class="sxs-lookup"><span data-stu-id="946e4-195">Usually, this is something like `/dev/eth0` (for your standard Ethernet interface) or `/dev/lo0` (for localhost traffic).</span></span> <span data-ttu-id="946e4-196">자세한 내용은 참조는 `tcpdump` 호스트 시스템에서 기본 페이지입니다.</span><span class="sxs-lookup"><span data-stu-id="946e4-196">For more information, see the `tcpdump` man page on your host system.</span></span>

## <a name="collect-a-network-trace-in-the-browser"></a><span data-ttu-id="946e4-197">브라우저에서 네트워크 추적을 수집 합니다.</span><span class="sxs-lookup"><span data-stu-id="946e4-197">Collect a network trace in the browser</span></span>

<span data-ttu-id="946e4-198">이 메서드는 브라우저 기반 앱 에서만 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="946e4-198">This method only works for browser-based apps.</span></span>

<span data-ttu-id="946e4-199">대부분의 브라우저 개발자 도구는 브라우저와 서버 간의 네트워크 활동을 캡처할 수 있는 "네트워크" 탭을 갖습니다.</span><span class="sxs-lookup"><span data-stu-id="946e4-199">Most browser Developer Tools have a "Network" tab that allows you to capture network activity between the browser and the server.</span></span> <span data-ttu-id="946e4-200">그러나 이러한 추적 WebSocket 및 Server-Sent 이벤트 메시지를 포함 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="946e4-200">However, these traces don't include WebSocket and Server-Sent Event messages.</span></span> <span data-ttu-id="946e4-201">이러한 전송을 사용 하는 경우 Fiddler 또는 TcpDump (아래 설명 참조)와 같은 도구를 사용 하는 더 나은 방법이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="946e4-201">If you are using those transports, using a tool like Fiddler or TcpDump (described below) is a better approach.</span></span>

### <a name="microsoft-edge-and-internet-explorer"></a><span data-ttu-id="946e4-202">Microsoft Edge 및 Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="946e4-202">Microsoft Edge and Internet Explorer</span></span>

<span data-ttu-id="946e4-203">(지침에 대해 동일 Edge와 Internet Explorer)</span><span class="sxs-lookup"><span data-stu-id="946e4-203">(The instructions are the same for both Edge and Internet Explorer)</span></span>

1. <span data-ttu-id="946e4-204">F12 키를 눌러 개발자 도구 열기</span><span class="sxs-lookup"><span data-stu-id="946e4-204">Press F12 to open the Dev Tools</span></span>
2. <span data-ttu-id="946e4-205">네트워크 탭을 클릭 합니다.</span><span class="sxs-lookup"><span data-stu-id="946e4-205">Click the Network Tab</span></span>
3. <span data-ttu-id="946e4-206">문제를 재현 및 (필요한 경우) 페이지를 새로 고칩니다.</span><span class="sxs-lookup"><span data-stu-id="946e4-206">Refresh the page (if needed) and reproduce the problem</span></span>
4. <span data-ttu-id="946e4-207">추적 "HAR" 파일로 내보내려면 도구 모음에서 저장 아이콘을 클릭 합니다.</span><span class="sxs-lookup"><span data-stu-id="946e4-207">Click the Save icon in the toolbar to export the trace as a "HAR" file:</span></span>

![저장 아이콘을 Microsoft Edge 개발자 도구 네트워크 탭](diagnostics/ie-edge-har-export.png)

### <a name="google-chrome"></a><span data-ttu-id="946e4-209">Google Chrome</span><span class="sxs-lookup"><span data-stu-id="946e4-209">Google Chrome</span></span>

1. <span data-ttu-id="946e4-210">F12 키를 눌러 개발자 도구 열기</span><span class="sxs-lookup"><span data-stu-id="946e4-210">Press F12 to open the Dev Tools</span></span>
2. <span data-ttu-id="946e4-211">네트워크 탭을 클릭 합니다.</span><span class="sxs-lookup"><span data-stu-id="946e4-211">Click the Network Tab</span></span>
3. <span data-ttu-id="946e4-212">문제를 재현 및 (필요한 경우) 페이지를 새로 고칩니다.</span><span class="sxs-lookup"><span data-stu-id="946e4-212">Refresh the page (if needed) and reproduce the problem</span></span>
4. <span data-ttu-id="946e4-213">선택한 요청 목록에서 아무 곳 이나 클릭 마우스 오른쪽 단추로 "Save as HAR with 콘텐츠":</span><span class="sxs-lookup"><span data-stu-id="946e4-213">Right click anywhere in the list of requests and choose "Save as HAR with content":</span></span>

![Google Chrome 개발자 도구 네트워크 탭에서 "콘텐츠를 사용 하 여 HAR로 저장" 옵션](diagnostics/chrome-har-export.png)

### <a name="mozilla-firefox"></a><span data-ttu-id="946e4-215">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="946e4-215">Mozilla Firefox</span></span>

1. <span data-ttu-id="946e4-216">F12 키를 눌러 개발자 도구 열기</span><span class="sxs-lookup"><span data-stu-id="946e4-216">Press F12 to open the Dev Tools</span></span>
2. <span data-ttu-id="946e4-217">네트워크 탭을 클릭 합니다.</span><span class="sxs-lookup"><span data-stu-id="946e4-217">Click the Network Tab</span></span>
3. <span data-ttu-id="946e4-218">문제를 재현 및 (필요한 경우) 페이지를 새로 고칩니다.</span><span class="sxs-lookup"><span data-stu-id="946e4-218">Refresh the page (if needed) and reproduce the problem</span></span>
4. <span data-ttu-id="946e4-219">"저장 모든으로 HAR" 요청 목록에서 아무 곳 이나 클릭 마우스 오른쪽 단추로 선택한</span><span class="sxs-lookup"><span data-stu-id="946e4-219">Right click anywhere in the list of requests and choose "Save All As HAR"</span></span>

![Mozilla Firefox 개발자 도구 네트워크 탭에서 "저장 HAR로 모두" 옵션](diagnostics/firefox-har-export.png)

## <a name="attach-diagnostics-files-to-github-issues"></a><span data-ttu-id="946e4-221">GitHub 문제를 진단 파일을 첨부 합니다.</span><span class="sxs-lookup"><span data-stu-id="946e4-221">Attach diagnostics files to GitHub issues</span></span>

<span data-ttu-id="946e4-222">GitHub 문제에 있어 바꾸어 진단 파일을 첨부할 수 있습니다를 `.txt` 확장 하 고 다음 끌어서 놓아 문제에 로그온 합니다.</span><span class="sxs-lookup"><span data-stu-id="946e4-222">You can attach Diagnostics files to GitHub issues by renaming them so they have a `.txt` extension and then dragging and dropping them on to the issue.</span></span>

> [!NOTE]
> <span data-ttu-id="946e4-223">GitHub 문제에서 네트워크 추적 또는 로그 파일의 내용을 붙여넣으세요 하지 마십시오.</span><span class="sxs-lookup"><span data-stu-id="946e4-223">Please don't paste the content of log files or network traces in GitHub issue.</span></span> <span data-ttu-id="946e4-224">이러한 로그 및 추적 매우 클 수 있으며 GitHub는 일반적으로 잘라내려면 합니다.</span><span class="sxs-lookup"><span data-stu-id="946e4-224">These logs and traces can be quite large and GitHub will usually truncate them.</span></span>

![GitHub 문제에 대 한 로그 파일을 끌어](diagnostics/attaching-diagnostics-files.png)

## <a name="additional-resources"></a><span data-ttu-id="946e4-226">추가 자료</span><span class="sxs-lookup"><span data-stu-id="946e4-226">Additional resources</span></span>

* <xref:signalr/configuration>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
