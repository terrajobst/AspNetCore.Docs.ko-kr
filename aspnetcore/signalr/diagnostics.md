---
title: ASP.NET Core SignalR의 로깅 및 진단
author: anurse
description: ASP.NET Core SignalR 앱에서 진단을 수집 하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: anurse
ms.custom: signalr
ms.date: 11/12/2019
no-loc:
- SignalR
uid: signalr/diagnostics
ms.openlocfilehash: c5bd2ac27f8ca486b0d75aed8439747f72448625
ms.sourcegitcommit: 3fc3020961e1289ee5bf5f3c365ce8304d8ebf19
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73963851"
---
# <a name="logging-and-diagnostics-in-aspnet-core-opno-locsignalr"></a><span data-ttu-id="3c9c6-103">ASP.NET Core SignalR의 로깅 및 진단</span><span class="sxs-lookup"><span data-stu-id="3c9c6-103">Logging and diagnostics in ASP.NET Core SignalR</span></span>

<span data-ttu-id="3c9c6-104">[Andrew Stanton-간호사](https://twitter.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="3c9c6-104">By [Andrew Stanton-Nurse](https://twitter.com/anurse)</span></span>

<span data-ttu-id="3c9c6-105">이 문서에서는 문제 해결을 위해 ASP.NET Core SignalR 앱에서 진단 정보를 수집 하기 위한 지침을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="3c9c6-105">This article provides guidance for gathering diagnostics from your ASP.NET Core SignalR app to help troubleshoot issues.</span></span>

## <a name="server-side-logging"></a><span data-ttu-id="3c9c6-106">서버 쪽 로깅</span><span class="sxs-lookup"><span data-stu-id="3c9c6-106">Server-side logging</span></span>

> [!WARNING]
> <span data-ttu-id="3c9c6-107">서버 쪽 로그에는 앱의 중요 한 정보가 포함 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3c9c6-107">Server-side logs may contain sensitive information from your app.</span></span> <span data-ttu-id="3c9c6-108">프로덕션 앱의 원시 로그를 GitHub와 같은 공용 포럼에 게시 **하지 마세요** .</span><span class="sxs-lookup"><span data-stu-id="3c9c6-108">**Never** post raw logs from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="3c9c6-109">SignalR은 ASP.NET Core의 일부 이므로 ASP.NET Core 로깅 시스템을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="3c9c6-109">Since SignalR is part of ASP.NET Core, it uses the ASP.NET Core logging system.</span></span> <span data-ttu-id="3c9c6-110">기본 구성에서 SignalR는 매우 적은 정보를 기록 하지만이를 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3c9c6-110">In the default configuration, SignalR logs very little information, but this can configured.</span></span> <span data-ttu-id="3c9c6-111">ASP.NET Core 로깅 구성에 대 한 자세한 내용은 [ASP.NET Core 로깅](xref:fundamentals/logging/index#configuration) 에 대 한 설명서를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="3c9c6-111">See the documentation on [ASP.NET Core logging](xref:fundamentals/logging/index#configuration) for details on configuring ASP.NET Core logging.</span></span>

SignalR<span data-ttu-id="3c9c6-112">는 두 개의로 거 범주를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="3c9c6-112"> uses two logger categories:</span></span>

* <span data-ttu-id="3c9c6-113">허브 프로토콜과 관련 된 로그, 허브 활성화, 메서드 호출 및 기타 허브 관련 활동에 대 한 &ndash;를 `Microsoft.AspNetCore.SignalR` 합니다.</span><span class="sxs-lookup"><span data-stu-id="3c9c6-113">`Microsoft.AspNetCore.SignalR` &ndash; for logs related to Hub Protocols, activating Hubs, invoking methods, and other Hub-related activities.</span></span>
* <span data-ttu-id="3c9c6-114">Websocket, 긴 폴링 및 서버에서 보낸 이벤트, 하위 수준 SignalR 인프라 등의 전송과 관련 된 로그에 대 한 &ndash;를 `Microsoft.AspNetCore.Http.Connections` 합니다.</span><span class="sxs-lookup"><span data-stu-id="3c9c6-114">`Microsoft.AspNetCore.Http.Connections` &ndash; for logs related to transports such as WebSockets, Long Polling and Server-Sent Events and low-level SignalR infrastructure.</span></span>

<span data-ttu-id="3c9c6-115">SignalR에서 자세한 로그를 사용 하도록 설정 하려면 `Logging`의 `LogLevel` 하위 섹션에 다음 항목을 추가 하 여 위의 접두사를 *appsettings* 파일의 `Debug` 수준으로 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="3c9c6-115">To enable detailed logs from SignalR, configure both of the preceding prefixes to the `Debug` level in your *appsettings.json* file by adding the following items to the `LogLevel` sub-section in `Logging`:</span></span>

[!code-json[](diagnostics/logging-config.json?highlight=7-8)]

<span data-ttu-id="3c9c6-116">`CreateWebHostBuilder` 메서드의 코드에서이를 구성할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3c9c6-116">You can also configure this in code in your `CreateWebHostBuilder` method:</span></span>

[!code-csharp[](diagnostics/logging-config-code.cs?highlight=5-6)]

<span data-ttu-id="3c9c6-117">JSON 기반 구성을 사용 하지 않는 경우 구성 시스템에서 다음 구성 값을 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="3c9c6-117">If you aren't using JSON-based configuration, set the following configuration values in your configuration system:</span></span>

* `Logging:LogLevel:Microsoft.AspNetCore.SignalR` = `Debug`
* `Logging:LogLevel:Microsoft.AspNetCore.Http.Connections` = `Debug`

<span data-ttu-id="3c9c6-118">구성 시스템에 대 한 설명서를 확인 하 여 중첩 된 구성 값을 지정 하는 방법을 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="3c9c6-118">Check the documentation for your configuration system to determine how to specify nested configuration values.</span></span> <span data-ttu-id="3c9c6-119">예를 들어 환경 변수를 사용 하는 경우 `:` 대신 두 개의 `_` 문자가 사용 됩니다 (예: `Logging__LogLevel__Microsoft.AspNetCore.SignalR`).</span><span class="sxs-lookup"><span data-stu-id="3c9c6-119">For example, when using environment variables, two `_` characters are used instead of the `:` (for example, `Logging__LogLevel__Microsoft.AspNetCore.SignalR`).</span></span>

<span data-ttu-id="3c9c6-120">앱에 대 한 자세한 진단을 수집할 때 `Debug` 수준을 사용 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="3c9c6-120">We recommend using the `Debug` level when gathering more detailed diagnostics for your app.</span></span> <span data-ttu-id="3c9c6-121">`Trace` 수준은 매우 낮은 수준의 진단을 생성 하며 앱에서 문제를 진단 하는 데 거의 필요 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="3c9c6-121">The `Trace` level produces very low-level diagnostics and is rarely needed to diagnose issues in your app.</span></span>

## <a name="access-server-side-logs"></a><span data-ttu-id="3c9c6-122">서버 쪽 로그 액세스</span><span class="sxs-lookup"><span data-stu-id="3c9c6-122">Access server-side logs</span></span>

<span data-ttu-id="3c9c6-123">서버 쪽 로그에 액세스 하는 방법은를 실행 하는 환경에 따라 달라 집니다.</span><span class="sxs-lookup"><span data-stu-id="3c9c6-123">How you access server-side logs depends on the environment in which you're running.</span></span>

### <a name="as-a-console-app-outside-iis"></a><span data-ttu-id="3c9c6-124">IIS 외부의 콘솔 앱으로</span><span class="sxs-lookup"><span data-stu-id="3c9c6-124">As a console app outside IIS</span></span>

<span data-ttu-id="3c9c6-125">콘솔 응용 프로그램에서 실행 하는 경우 [콘솔로 거](xref:fundamentals/logging/index#console-provider) 를 기본적으로 사용 하도록 설정 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="3c9c6-125">If you're running in a console app, the [Console logger](xref:fundamentals/logging/index#console-provider) should be enabled by default.</span></span> SignalR<span data-ttu-id="3c9c6-126"> 로그가 콘솔에 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="3c9c6-126"> logs will appear in the console.</span></span>

### <a name="within-iis-express-from-visual-studio"></a><span data-ttu-id="3c9c6-127">Visual Studio의 IIS Express 내에서</span><span class="sxs-lookup"><span data-stu-id="3c9c6-127">Within IIS Express from Visual Studio</span></span>

<span data-ttu-id="3c9c6-128">Visual Studio에서 로그 출력이 **출력** 창에 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="3c9c6-128">Visual Studio displays the log output in the **Output** window.</span></span> <span data-ttu-id="3c9c6-129">**웹 서버 ASP.NET Core** 드롭다운 옵션을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="3c9c6-129">Select the **ASP.NET Core Web Server** drop down option.</span></span>

### <a name="azure-app-service"></a><span data-ttu-id="3c9c6-130">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="3c9c6-130">Azure App Service</span></span>

<span data-ttu-id="3c9c6-131">Azure App Service 포털의 **진단 로그** 섹션에서 **응용 프로그램 로깅 (파일 시스템)** 옵션을 사용 하도록 설정 하 고 `Verbose`**수준을** 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="3c9c6-131">Enable the **Application Logging (Filesystem)** option in the **Diagnostics logs** section of the Azure App Service portal and configure the **Level** to `Verbose`.</span></span> <span data-ttu-id="3c9c6-132">로그 **스트리밍** 서비스와 App Service 파일 시스템의 로그에서 로그를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3c9c6-132">Logs should be available from the **Log streaming** service and in logs on the file system of the App Service.</span></span> <span data-ttu-id="3c9c6-133">자세한 내용은 [Azure 로그 스트리밍](xref:fundamentals/logging/index#azure-log-streaming)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="3c9c6-133">For more information, see [Azure log streaming](xref:fundamentals/logging/index#azure-log-streaming).</span></span>

### <a name="other-environments"></a><span data-ttu-id="3c9c6-134">기타 환경</span><span class="sxs-lookup"><span data-stu-id="3c9c6-134">Other environments</span></span>

<span data-ttu-id="3c9c6-135">앱을 다른 환경 (예: Docker, Kubernetes 또는 Windows 서비스)에 배포 하는 경우 환경에 적합 한 로깅 공급자를 구성 하는 방법에 대 한 자세한 내용은 <xref:fundamentals/logging/index>를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="3c9c6-135">If the app is deployed to another environment (for example, Docker, Kubernetes, or Windows Service), see <xref:fundamentals/logging/index> for more information on how to configure logging providers suitable for the environment.</span></span>

## <a name="javascript-client-logging"></a><span data-ttu-id="3c9c6-136">JavaScript 클라이언트 로깅</span><span class="sxs-lookup"><span data-stu-id="3c9c6-136">JavaScript client logging</span></span>

> [!WARNING]
> <span data-ttu-id="3c9c6-137">클라이언트 쪽 로그에는 앱의 중요 한 정보가 포함 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3c9c6-137">Client-side logs may contain sensitive information from your app.</span></span> <span data-ttu-id="3c9c6-138">프로덕션 앱의 원시 로그를 GitHub와 같은 공용 포럼에 게시 **하지 마세요** .</span><span class="sxs-lookup"><span data-stu-id="3c9c6-138">**Never** post raw logs from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="3c9c6-139">JavaScript 클라이언트를 사용 하는 경우 `HubConnectionBuilder`에서 `configureLogging` 메서드를 사용 하 여 로깅 옵션을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3c9c6-139">When using the JavaScript client, you can configure logging options using the `configureLogging` method on `HubConnectionBuilder`:</span></span>

[!code-javascript[](diagnostics/logging-config-js.js?highlight=3)]

<span data-ttu-id="3c9c6-140">로깅을 완전히 사용 하지 않도록 설정 하려면 `configureLogging` 메서드에서 `signalR.LogLevel.None`를 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="3c9c6-140">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="3c9c6-141">다음 표에서는 JavaScript 클라이언트에서 사용할 수 있는 로그 수준을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="3c9c6-141">The following table shows log levels available to the JavaScript client.</span></span> <span data-ttu-id="3c9c6-142">로그 수준을 이러한 값 중 하나로 설정 하면 해당 수준 및 테이블 위의 모든 수준에서 로깅을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3c9c6-142">Setting the log level to one of these values enables logging at that level and all levels above it in the table.</span></span>

| <span data-ttu-id="3c9c6-143">Level</span><span class="sxs-lookup"><span data-stu-id="3c9c6-143">Level</span></span> | <span data-ttu-id="3c9c6-144">설명</span><span class="sxs-lookup"><span data-stu-id="3c9c6-144">Description</span></span> |
| ----- | ----------- |
| `None` | <span data-ttu-id="3c9c6-145">메시지가 기록 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="3c9c6-145">No messages are logged.</span></span> |
| `Critical` | <span data-ttu-id="3c9c6-146">전체 앱에서 오류를 나타내는 메시지입니다.</span><span class="sxs-lookup"><span data-stu-id="3c9c6-146">Messages that indicate a failure in the entire app.</span></span> |
| `Error` | <span data-ttu-id="3c9c6-147">현재 작업의 실패를 나타내는 메시지입니다.</span><span class="sxs-lookup"><span data-stu-id="3c9c6-147">Messages that indicate a failure in the current operation.</span></span> |
| `Warning` | <span data-ttu-id="3c9c6-148">심각 하지 않은 문제를 나타내는 메시지입니다.</span><span class="sxs-lookup"><span data-stu-id="3c9c6-148">Messages that indicate a non-fatal problem.</span></span> |
| `Information` | <span data-ttu-id="3c9c6-149">정보 메시지.</span><span class="sxs-lookup"><span data-stu-id="3c9c6-149">Informational messages.</span></span> |
| `Debug` | <span data-ttu-id="3c9c6-150">디버깅에 유용한 진단 메시지입니다.</span><span class="sxs-lookup"><span data-stu-id="3c9c6-150">Diagnostic messages useful for debugging.</span></span> |
| `Trace` | <span data-ttu-id="3c9c6-151">특정 문제를 진단 하기 위해 설계 된 매우 자세한 진단 메시지입니다.</span><span class="sxs-lookup"><span data-stu-id="3c9c6-151">Very detailed diagnostic messages designed for diagnosing specific issues.</span></span> |

<span data-ttu-id="3c9c6-152">자세한 정도를 구성 하면 로그가 브라우저 콘솔에 기록 되거나 NodeJS 앱의 표준 출력에 기록 됩니다.</span><span class="sxs-lookup"><span data-stu-id="3c9c6-152">Once you've configured the verbosity, the logs will be written to the Browser Console (or Standard Output in a NodeJS app).</span></span>

<span data-ttu-id="3c9c6-153">로그를 사용자 지정 로깅 시스템으로 전송 하려는 경우 `ILogger` 인터페이스를 구현 하는 JavaScript 개체를 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3c9c6-153">If you want to send logs to a custom logging system, you can provide a JavaScript object implementing the `ILogger` interface.</span></span> <span data-ttu-id="3c9c6-154">구현 해야 하는 유일한 방법은 이벤트의 수준 및 이벤트와 연결 된 메시지를 사용 하는 `log`입니다.</span><span class="sxs-lookup"><span data-stu-id="3c9c6-154">The only method that needs to be implemented is `log`, which takes the level of the event and the message associated with the event.</span></span> <span data-ttu-id="3c9c6-155">예를 들어 다음과 같은 가치를 제공해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="3c9c6-155">For example:</span></span>

[!code-typescript[](diagnostics/custom-logger.ts?highlight=3-7,13)]

## <a name="net-client-logging"></a><span data-ttu-id="3c9c6-156">.NET 클라이언트 로깅</span><span class="sxs-lookup"><span data-stu-id="3c9c6-156">.NET client logging</span></span>

> [!WARNING]
> <span data-ttu-id="3c9c6-157">클라이언트 쪽 로그에는 앱의 중요 한 정보가 포함 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3c9c6-157">Client-side logs may contain sensitive information from your app.</span></span> <span data-ttu-id="3c9c6-158">프로덕션 앱의 원시 로그를 GitHub와 같은 공용 포럼에 게시 **하지 마세요** .</span><span class="sxs-lookup"><span data-stu-id="3c9c6-158">**Never** post raw logs from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="3c9c6-159">.NET 클라이언트에서 로그를 가져오기 위해 `HubConnectionBuilder`에서 `ConfigureLogging` 메서드를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3c9c6-159">To get logs from the .NET client, you can use the `ConfigureLogging` method on `HubConnectionBuilder`.</span></span> <span data-ttu-id="3c9c6-160">`WebHostBuilder` 및 `HostBuilder`에서 `ConfigureLogging` 메서드와 동일한 방식으로 작동 합니다.</span><span class="sxs-lookup"><span data-stu-id="3c9c6-160">This works the same way as the `ConfigureLogging` method on `WebHostBuilder` and `HostBuilder`.</span></span> <span data-ttu-id="3c9c6-161">ASP.NET Core에서 사용 하는 것과 동일한 로깅 공급자를 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3c9c6-161">You can configure the same logging providers you use in ASP.NET Core.</span></span> <span data-ttu-id="3c9c6-162">그러나 개별 로깅 공급자에 대 한 NuGet 패키지를 수동으로 설치 하 고 사용 하도록 설정 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="3c9c6-162">However, you have to manually install and enable the NuGet packages for the individual logging providers.</span></span>

### <a name="console-logging"></a><span data-ttu-id="3c9c6-163">콘솔 로깅</span><span class="sxs-lookup"><span data-stu-id="3c9c6-163">Console logging</span></span>

<span data-ttu-id="3c9c6-164">콘솔 로깅을 사용 하도록 설정 하려면 [Microsoft 확장명이 console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) 패키지를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="3c9c6-164">In order to enable Console logging, add the [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) package.</span></span> <span data-ttu-id="3c9c6-165">그런 다음 `AddConsole` 메서드를 사용 하 여 콘솔로 거를 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="3c9c6-165">Then, use the `AddConsole` method to configure the console logger:</span></span>

[!code-csharp[](diagnostics/net-client-console-log.cs?highlight=6)]

### <a name="debug-output-window-logging"></a><span data-ttu-id="3c9c6-166">디버그 출력 창 로깅</span><span class="sxs-lookup"><span data-stu-id="3c9c6-166">Debug output window logging</span></span>

<span data-ttu-id="3c9c6-167">Visual Studio의 **출력** 창으로 이동 하도록 로그를 구성할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3c9c6-167">You can also configure logs to go to the **Output** window in Visual Studio.</span></span> <span data-ttu-id="3c9c6-168">[Microsoft Extensions. Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) 패키지를 설치 하 고 `AddDebug` 메서드를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="3c9c6-168">Install the [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) package and use the `AddDebug` method:</span></span>

[!code-csharp[](diagnostics/net-client-debug-log.cs?highlight=6)]

### <a name="other-logging-providers"></a><span data-ttu-id="3c9c6-169">기타 로깅 공급자</span><span class="sxs-lookup"><span data-stu-id="3c9c6-169">Other logging providers</span></span>

SignalR<span data-ttu-id="3c9c6-170"> Serilog, Seq, NLog 또는 `Microsoft.Extensions.Logging`와 통합 되는 기타 로깅 시스템과 같은 다른 로깅 공급자를 지원 합니다.</span><span class="sxs-lookup"><span data-stu-id="3c9c6-170"> supports other logging providers such as Serilog, Seq, NLog, or any other logging system that integrates with `Microsoft.Extensions.Logging`.</span></span> <span data-ttu-id="3c9c6-171">로깅 시스템에서 `ILoggerProvider`제공 하는 경우 `AddProvider`에 등록할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3c9c6-171">If your logging system provides an `ILoggerProvider`, you can register it with `AddProvider`:</span></span>

[!code-csharp[](diagnostics/net-client-custom-log.cs?highlight=6)]

### <a name="control-verbosity"></a><span data-ttu-id="3c9c6-172">컨트롤 자세한 정도</span><span class="sxs-lookup"><span data-stu-id="3c9c6-172">Control verbosity</span></span>

<span data-ttu-id="3c9c6-173">앱의 다른 위치에서 로깅하는 경우 기본 수준을 `Debug`로 변경 하는 것이 너무 자세한 것일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3c9c6-173">If you are logging from other places in your app, changing the default level to `Debug` may be too verbose.</span></span> <span data-ttu-id="3c9c6-174">필터를 사용 하 여 SignalR 로그의 로깅 수준을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3c9c6-174">You can use a Filter to configure the logging level for SignalR logs.</span></span> <span data-ttu-id="3c9c6-175">이 작업은 서버에서와 거의 동일한 방식으로 코드에서 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3c9c6-175">This can be done in code, in much the same way as on the server:</span></span>

[!code-csharp[Controlling verbosity in .NET client](diagnostics/logging-config-client-code.cs?highlight=9-10)]

## <a name="network-traces"></a><span data-ttu-id="3c9c6-176">네트워크 추적</span><span class="sxs-lookup"><span data-stu-id="3c9c6-176">Network traces</span></span>

> [!WARNING]
> <span data-ttu-id="3c9c6-177">네트워크 추적에는 앱에서 보낸 모든 메시지의 전체 콘텐츠가 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="3c9c6-177">A network trace contains the full contents of every message sent by your app.</span></span> <span data-ttu-id="3c9c6-178">프로덕션 앱에서 GitHub와 같은 공용 포럼에 원시 네트워크 추적을 게시 **하지 마세요** .</span><span class="sxs-lookup"><span data-stu-id="3c9c6-178">**Never** post raw network traces from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="3c9c6-179">문제가 발생 하는 경우 네트워크 추적이 많은 유용한 정보를 제공 하는 경우가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3c9c6-179">If you encounter an issue, a network trace can sometimes provide a lot of helpful information.</span></span> <span data-ttu-id="3c9c6-180">문제 추적기에서 문제를 해결 하려는 경우 특히 유용 합니다.</span><span class="sxs-lookup"><span data-stu-id="3c9c6-180">This is particularly useful if you're going to file an issue on our issue tracker.</span></span>

## <a name="collect-a-network-trace-with-fiddler-preferred-option"></a><span data-ttu-id="3c9c6-181">Fiddler를 사용 하 여 네트워크 추적 수집 (기본 설정 옵션)</span><span class="sxs-lookup"><span data-stu-id="3c9c6-181">Collect a network trace with Fiddler (preferred option)</span></span>

<span data-ttu-id="3c9c6-182">이 메서드는 모든 앱에 대해 작동 합니다.</span><span class="sxs-lookup"><span data-stu-id="3c9c6-182">This method works for all apps.</span></span>

<span data-ttu-id="3c9c6-183">Fiddler는 HTTP 추적을 수집 하는 매우 강력한 도구입니다.</span><span class="sxs-lookup"><span data-stu-id="3c9c6-183">Fiddler is a very powerful tool for collecting HTTP traces.</span></span> <span data-ttu-id="3c9c6-184">[Telerik.com/fiddler](https://www.telerik.com/fiddler)에서 설치 하 고 시작 하 여 앱을 실행 하 고 문제를 재현 합니다.</span><span class="sxs-lookup"><span data-stu-id="3c9c6-184">Install it from [telerik.com/fiddler](https://www.telerik.com/fiddler), launch it, and then run your app and reproduce the issue.</span></span> <span data-ttu-id="3c9c6-185">Fiddler는 Windows에서 사용할 수 있으며 macOS 및 Linux에 대 한 베타 버전이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3c9c6-185">Fiddler is available for Windows, and there are beta versions for macOS and Linux.</span></span>

<span data-ttu-id="3c9c6-186">HTTPS를 사용 하 여 연결 하는 경우 Fiddler에서 HTTPS 트래픽을 해독할 수 있는지 확인 하는 몇 가지 추가 단계가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3c9c6-186">If you connect using HTTPS, there are some extra steps to ensure Fiddler can decrypt the HTTPS traffic.</span></span> <span data-ttu-id="3c9c6-187">자세한 내용은 [Fiddler 설명서](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="3c9c6-187">For more details, see the [Fiddler documentation](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS).</span></span>

<span data-ttu-id="3c9c6-188">추적을 수집한 후에는 메뉴 모음에서 **파일** > **저장** > **모든 세션** 을 선택 하 여 추적을 내보낼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3c9c6-188">Once you've collected the trace, you can export the trace by choosing **File** > **Save** > **All Sessions** from the menu bar.</span></span>

![Fiddler에서 모든 세션 내보내기](diagnostics/fiddler-export.png)

## <a name="collect-a-network-trace-with-tcpdump-macos-and-linux-only"></a><span data-ttu-id="3c9c6-190">Tcpdump를 사용 하 여 네트워크 추적 수집 (macOS 및 Linux에만 해당)</span><span class="sxs-lookup"><span data-stu-id="3c9c6-190">Collect a network trace with tcpdump (macOS and Linux only)</span></span>

<span data-ttu-id="3c9c6-191">이 메서드는 모든 앱에 대해 작동 합니다.</span><span class="sxs-lookup"><span data-stu-id="3c9c6-191">This method works for all apps.</span></span>

<span data-ttu-id="3c9c6-192">명령 셸에서 다음 명령을 실행 하 여 tcpdump를 사용 하 여 원시 TCP 추적을 수집할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3c9c6-192">You can collect raw TCP traces using tcpdump by running the following command from a command shell.</span></span> <span data-ttu-id="3c9c6-193">사용 권한 오류가 발생 하면 `root` 하거나 명령에 접두사를 사용 하 여 `sudo` 해야 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3c9c6-193">You may need to be `root` or prefix the command with `sudo` if you get a permissions error:</span></span>

```console
tcpdump -i [interface] -w trace.pcap
```

<span data-ttu-id="3c9c6-194">`[interface]`를 캡처할 네트워크 인터페이스로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="3c9c6-194">Replace `[interface]` with the network interface you wish to capture on.</span></span> <span data-ttu-id="3c9c6-195">일반적으로 `/dev/eth0` (표준 이더넷 인터페이스의 경우) 또는 `/dev/lo0` (localhost 트래픽용)와 같은 경우입니다.</span><span class="sxs-lookup"><span data-stu-id="3c9c6-195">Usually, this is something like `/dev/eth0` (for your standard Ethernet interface) or `/dev/lo0` (for localhost traffic).</span></span> <span data-ttu-id="3c9c6-196">자세한 내용은 호스트 시스템의 `tcpdump` 매뉴얼 페이지를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="3c9c6-196">For more information, see the `tcpdump` man page on your host system.</span></span>

## <a name="collect-a-network-trace-in-the-browser"></a><span data-ttu-id="3c9c6-197">브라우저에서 네트워크 추적 수집</span><span class="sxs-lookup"><span data-stu-id="3c9c6-197">Collect a network trace in the browser</span></span>

<span data-ttu-id="3c9c6-198">이 메서드는 브라우저 기반 앱 에서만 작동 합니다.</span><span class="sxs-lookup"><span data-stu-id="3c9c6-198">This method only works for browser-based apps.</span></span>

<span data-ttu-id="3c9c6-199">대부분의 브라우저 개발자 도구 브라우저와 서버 간의 네트워크 활동을 캡처할 수 있는 "네트워크" 탭이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3c9c6-199">Most browser Developer Tools have a "Network" tab that allows you to capture network activity between the browser and the server.</span></span> <span data-ttu-id="3c9c6-200">그러나 이러한 추적에는 WebSocket 및 서버에서 보낸 이벤트 메시지가 포함 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="3c9c6-200">However, these traces don't include WebSocket and Server-Sent Event messages.</span></span> <span data-ttu-id="3c9c6-201">이러한 전송을 사용 하는 경우 Fiddler 또는 TcpDump와 같은 도구 (아래 설명 참조)를 사용 하는 것이 더 나은 방법입니다.</span><span class="sxs-lookup"><span data-stu-id="3c9c6-201">If you are using those transports, using a tool like Fiddler or TcpDump (described below) is a better approach.</span></span>

### <a name="microsoft-edge-and-internet-explorer"></a><span data-ttu-id="3c9c6-202">Microsoft Edge 및 Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="3c9c6-202">Microsoft Edge and Internet Explorer</span></span>

<span data-ttu-id="3c9c6-203">에 대 한 지침은 Edge 및 Internet Explorer 모두에 대해 동일 합니다.</span><span class="sxs-lookup"><span data-stu-id="3c9c6-203">(The instructions are the same for both Edge and Internet Explorer)</span></span>

1. <span data-ttu-id="3c9c6-204">F12 키를 눌러 개발자 도구 열기</span><span class="sxs-lookup"><span data-stu-id="3c9c6-204">Press F12 to open the Dev Tools</span></span>
2. <span data-ttu-id="3c9c6-205">네트워크 탭을 클릭 합니다.</span><span class="sxs-lookup"><span data-stu-id="3c9c6-205">Click the Network Tab</span></span>
3. <span data-ttu-id="3c9c6-206">페이지를 새로 고치고 (필요한 경우) 문제를 재현 합니다.</span><span class="sxs-lookup"><span data-stu-id="3c9c6-206">Refresh the page (if needed) and reproduce the problem</span></span>
4. <span data-ttu-id="3c9c6-207">도구 모음에서 저장 아이콘을 클릭 하 여 추적을 "HAR" 파일로 내보냅니다.</span><span class="sxs-lookup"><span data-stu-id="3c9c6-207">Click the Save icon in the toolbar to export the trace as a "HAR" file:</span></span>

![Microsoft Edge Dev Tools 네트워크 탭의 저장 아이콘](diagnostics/ie-edge-har-export.png)

### <a name="google-chrome"></a><span data-ttu-id="3c9c6-209">Google Chrome</span><span class="sxs-lookup"><span data-stu-id="3c9c6-209">Google Chrome</span></span>

1. <span data-ttu-id="3c9c6-210">F12 키를 눌러 개발자 도구 열기</span><span class="sxs-lookup"><span data-stu-id="3c9c6-210">Press F12 to open the Dev Tools</span></span>
2. <span data-ttu-id="3c9c6-211">네트워크 탭을 클릭 합니다.</span><span class="sxs-lookup"><span data-stu-id="3c9c6-211">Click the Network Tab</span></span>
3. <span data-ttu-id="3c9c6-212">페이지를 새로 고치고 (필요한 경우) 문제를 재현 합니다.</span><span class="sxs-lookup"><span data-stu-id="3c9c6-212">Refresh the page (if needed) and reproduce the problem</span></span>
4. <span data-ttu-id="3c9c6-213">요청 목록에서 아무 곳 이나 마우스 오른쪽 단추로 클릭 하 고 "콘텐츠를 사용 하 여 HAR로 저장"을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="3c9c6-213">Right click anywhere in the list of requests and choose "Save as HAR with content":</span></span>

![Google Chrome Dev Tools 네트워크 탭의 "콘텐츠를 사용 하 여 HAR로 저장" 옵션](diagnostics/chrome-har-export.png)

### <a name="mozilla-firefox"></a><span data-ttu-id="3c9c6-215">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="3c9c6-215">Mozilla Firefox</span></span>

1. <span data-ttu-id="3c9c6-216">F12 키를 눌러 개발자 도구 열기</span><span class="sxs-lookup"><span data-stu-id="3c9c6-216">Press F12 to open the Dev Tools</span></span>
2. <span data-ttu-id="3c9c6-217">네트워크 탭을 클릭 합니다.</span><span class="sxs-lookup"><span data-stu-id="3c9c6-217">Click the Network Tab</span></span>
3. <span data-ttu-id="3c9c6-218">페이지를 새로 고치고 (필요한 경우) 문제를 재현 합니다.</span><span class="sxs-lookup"><span data-stu-id="3c9c6-218">Refresh the page (if needed) and reproduce the problem</span></span>
4. <span data-ttu-id="3c9c6-219">요청 목록에서 아무 곳 이나 마우스 오른쪽 단추로 클릭 하 고 "모두 HAR로 저장"을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="3c9c6-219">Right click anywhere in the list of requests and choose "Save All As HAR"</span></span>

![Mozilla Firefox Dev Tools 네트워크 탭에서 "모든 것을 HAR로 저장" 옵션](diagnostics/firefox-har-export.png)

## <a name="attach-diagnostics-files-to-github-issues"></a><span data-ttu-id="3c9c6-221">진단 파일을 GitHub 문제에 연결</span><span class="sxs-lookup"><span data-stu-id="3c9c6-221">Attach diagnostics files to GitHub issues</span></span>

<span data-ttu-id="3c9c6-222">`.txt` 확장을 포함 하 고 문제에 끌어서 놓으면 진단 파일의 이름을 바꿔서 GitHub 문제에 연결할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3c9c6-222">You can attach Diagnostics files to GitHub issues by renaming them so they have a `.txt` extension and then dragging and dropping them on to the issue.</span></span>

> [!NOTE]
> <span data-ttu-id="3c9c6-223">GitHub 문제에 로그 파일 또는 네트워크 추적의 내용을 붙여 넣지 마세요.</span><span class="sxs-lookup"><span data-stu-id="3c9c6-223">Please don't paste the content of log files or network traces into a GitHub issue.</span></span> <span data-ttu-id="3c9c6-224">이러한 로그와 추적은 매우 클 수 있으며 GitHub는 일반적으로이를 자릅니다.</span><span class="sxs-lookup"><span data-stu-id="3c9c6-224">These logs and traces can be quite large, and GitHub usually truncates them.</span></span>

![GitHub 문제로 로그 파일 끌기](diagnostics/attaching-diagnostics-files.png)

## <a name="additional-resources"></a><span data-ttu-id="3c9c6-226">추가 자료</span><span class="sxs-lookup"><span data-stu-id="3c9c6-226">Additional resources</span></span>

* <xref:signalr/configuration>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
