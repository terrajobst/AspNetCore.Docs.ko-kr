---
title: ASP.NET Core SignalR 구성
author: bradygaster
description: ASP.NET Core SignalR 앱을 구성 하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 12/10/2019
no-loc:
- SignalR
uid: signalr/configuration
ms.openlocfilehash: c225ff88110dc17185a430ac1c422d2433306115
ms.sourcegitcommit: 2cb857f0de774df421e35289662ba92cfe56ffd1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75358114"
---
# <a name="aspnet-core-opno-locsignalr-configuration"></a><span data-ttu-id="d63a3-103">ASP.NET Core SignalR 구성</span><span class="sxs-lookup"><span data-stu-id="d63a3-103">ASP.NET Core SignalR configuration</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="d63a3-104">JSON/MessagePack serialization 옵션</span><span class="sxs-lookup"><span data-stu-id="d63a3-104">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="d63a3-105">ASP.NET Core SignalR는 메시지 인코딩에 [JSON](https://www.json.org/) 및 [MessagePack](https://msgpack.org/index.html)의 두 가지 프로토콜을 지원 합니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-105">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="d63a3-106">각 프로토콜에는 serialization 구성 옵션이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-106">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="d63a3-107">JSON serialization은 [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) 확장 메서드를 사용 하 여 서버에서 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-107">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="d63a3-108">`AddJsonProtocol`는 `Startup.ConfigureServices`[AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) 뒤에 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-108">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="d63a3-109">`AddJsonProtocol` 메서드는 `options` 개체를 전달받는 대리자를 받습니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-109">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="d63a3-110">해당 개체의 [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) 속성은 인수 및 반환 값의 serialization을 구성 하는 데 사용할 수 있는 `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> 개체입니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-110">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="d63a3-111">자세한 내용은 [system.object 설명서](/dotnet/api/system.text.json)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="d63a3-111">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="d63a3-112">예를 들어 기본 "camelCase" 이름 대신 속성 이름의 대/소문자를 변경 하지 않도록 serializer를 구성 하려면 `Startup.ConfigureServices`에서 다음 코드를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-112">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="d63a3-113">.NET 클라이언트에서는 [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)에 동일한 `AddJsonProtocol` 확장 메서드가 존재합니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-113">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="d63a3-114">이 확장 메서드를 확인하려면 `Microsoft.Extensions.DependencyInjection` 네임스페이스를 임포트해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-114">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

```csharp
// At the top of the file:
using Microsoft.Extensions.DependencyInjection;

// When constructing your connection:
var connection = new HubConnectionBuilder()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    })
    .Build();
```

> [!NOTE]
> <span data-ttu-id="d63a3-115">현재 JavaScript 클라이언트에서는 JSON 직렬화를 구성할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-115">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="d63a3-116">Newtonsoft.json로 전환 합니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-116">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="d63a3-117">`System.Text.Json`에서 지원 되지 않는 `Newtonsoft.Json` 기능이 필요한 경우 [newtonsoft.json으로 전환](xref:migration/22-to-30#switch-to-newtonsoftjson)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="d63a3-117">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="d63a3-118">MessagePack serialization 옵션</span><span class="sxs-lookup"><span data-stu-id="d63a3-118">MessagePack serialization options</span></span>

<span data-ttu-id="d63a3-119">MessagePack 직렬화는 [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) 호출에 대리자를 제공하여 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-119">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="d63a3-120">자세한 내용은 [SignalR의 MessagePack를](xref:signalr/messagepackhubprotocol) 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="d63a3-120">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="d63a3-121">현재 JavaScript 클라이언트에서는 MessagePack 직렬화를 구성할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-121">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="d63a3-122">서버 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="d63a3-122">Configure server options</span></span>

<span data-ttu-id="d63a3-123">다음 표에서 SignalR 허브를 구성 하는 옵션을 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-123">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="d63a3-124">옵션</span><span class="sxs-lookup"><span data-stu-id="d63a3-124">Option</span></span> | <span data-ttu-id="d63a3-125">기본값</span><span class="sxs-lookup"><span data-stu-id="d63a3-125">Default Value</span></span> | <span data-ttu-id="d63a3-126">설명</span><span class="sxs-lookup"><span data-stu-id="d63a3-126">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="d63a3-127">30초</span><span class="sxs-lookup"><span data-stu-id="d63a3-127">30 seconds</span></span> | <span data-ttu-id="d63a3-128">서버는이 간격 내에 메시지를 받지 못한 경우 (keep-alive 포함) 클라이언트의 연결을 해제 하는 것으로 간주 합니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-128">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="d63a3-129">이를 구현 하는 방법으로 인해 클라이언트에서 실제로 연결이 끊어진 것으로 표시 되는 시간 제한 간격 보다 오래 걸릴 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-129">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="d63a3-130">권장 값은 두 개의 `KeepAliveInterval` 값입니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-130">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="d63a3-131">15초</span><span class="sxs-lookup"><span data-stu-id="d63a3-131">15 seconds</span></span> | <span data-ttu-id="d63a3-132">클라이언트가 이 시간 제한 내에 초기 핸드셰이크 메시지를 전송하지 않으면 연결이 닫힙니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-132">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="d63a3-133">이 설정은 심각한 네트워크 지연으로 인해 핸드셰이크 시간 제한 오류가 발생하는 경우에만 수정해야 하는 고급 설정입니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-133">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="d63a3-134">핸드셰이크 프로세스에 대 한 자세한 내용은 [SignalR 허브 프로토콜 사양](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="d63a3-134">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="d63a3-135">15초</span><span class="sxs-lookup"><span data-stu-id="d63a3-135">15 seconds</span></span> | <span data-ttu-id="d63a3-136">서버가 이 간격 내에 메시지를 전송하지 않으면 자동으로 ping 메시지가 전송되어 연결이 열린 상태로 유지됩니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-136">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="d63a3-137">`KeepAliveInterval`을 변경할 경우 클라이언트의 `ServerTimeout`/`serverTimeoutInMilliseconds` 설정도 변경하십시오.</span><span class="sxs-lookup"><span data-stu-id="d63a3-137">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="d63a3-138">권장되는 `ServerTimeout`/`serverTimeoutInMilliseconds` 값은 `KeepAliveInterval` 값의 두 배입니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-138">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="d63a3-139">설치된 모든 프로토콜</span><span class="sxs-lookup"><span data-stu-id="d63a3-139">All installed protocols</span></span> | <span data-ttu-id="d63a3-140">허브가 지원하는 프로토콜입니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-140">Protocols supported by this hub.</span></span> <span data-ttu-id="d63a3-141">기본적으로 서버에 등록된 모든 프로토콜이 허용되지만 이 목록에서 프로토콜을 제거하여 개별 허브에 대해 특정 프로토콜을 비활성화시킬 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-141">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="d63a3-142">이 값이 `true`면 Hub 메서드에서 예외가 발생할 경우 자세한 예외 메시지가 클라이언트로 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-142">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="d63a3-143">예외 메시지에는 민감한 정보가 포함되어 있을 수 있으므로 기본값은 `false`입니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-143">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="d63a3-144">클라이언트 업로드 스트림에 대해 버퍼링 할 수 있는 최대 항목 수입니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-144">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="d63a3-145">이 제한에 도달 하면 서버에서 스트림 항목을 처리할 때까지 호출 처리가 차단 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-145">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="d63a3-146">32KB</span><span class="sxs-lookup"><span data-stu-id="d63a3-146">32 KB</span></span> | <span data-ttu-id="d63a3-147">들어오는 단일 허브 메시지의 최대 크기입니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-147">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="d63a3-148">`Startup.ConfigureServices`에서 `AddSignalR` 호출에 옵션 대리자를 제공하여 모든 허브에 대한 옵션을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-148">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSignalR(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

<span data-ttu-id="d63a3-149">단일 허브에 대 한 옵션은 `AddSignalR`에서 제공 하는 전역 옵션을 재정의 하며 <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>를 사용 하 여 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-149">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="d63a3-150">고급 HTTP 구성 옵션</span><span class="sxs-lookup"><span data-stu-id="d63a3-150">Advanced HTTP configuration options</span></span>

<span data-ttu-id="d63a3-151">전송 및 메모리 버퍼 관리와 관련된 고급 설정을 구성하려면 `HttpConnectionDispatcherOptions`를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-151">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="d63a3-152">이러한 옵션은 `Startup.Configure`에서 [\<t >의 Maphub](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) 에 대리자를 전달 하 여 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-152">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseRouting();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapHub<MyHub>("/myhub", options =>
        {
            options.Transports =
                HttpTransportType.WebSockets |
                HttpTransportType.LongPolling;
        });
    });
}
```

<span data-ttu-id="d63a3-153">다음 표에서는 ASP.NET Core SignalR의 고급 HTTP 옵션을 구성 하는 옵션을 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-153">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="d63a3-154">옵션</span><span class="sxs-lookup"><span data-stu-id="d63a3-154">Option</span></span> | <span data-ttu-id="d63a3-155">기본값</span><span class="sxs-lookup"><span data-stu-id="d63a3-155">Default Value</span></span> | <span data-ttu-id="d63a3-156">설명</span><span class="sxs-lookup"><span data-stu-id="d63a3-156">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="d63a3-157">32KB</span><span class="sxs-lookup"><span data-stu-id="d63a3-157">32 KB</span></span> | <span data-ttu-id="d63a3-158">클라이언트에서 수신 된 백 압력을 적용 하기 전에 버퍼링 되는 최대 바이트 수입니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-158">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="d63a3-159">이 값을 늘리면 서버는 백 압력을 적용 하지 않고 더 큰 메시지를 더 빨리 수신할 수 있지만 메모리 사용을 높일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-159">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="d63a3-160">허브 클래스에 적용된 `Authorize` 특성에서 자동으로 수집된 데이터입니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-160">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="d63a3-161">클라이언트가 허브에 연결할 권한이 있는지 확인하는 데 사용되는 [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) 개체의 목록입니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-161">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="d63a3-162">32KB</span><span class="sxs-lookup"><span data-stu-id="d63a3-162">32 KB</span></span> | <span data-ttu-id="d63a3-163">서버에서 백 압력을 관찰 하기 전에 버퍼링 하는 앱에서 보낸 최대 바이트 수입니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-163">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="d63a3-164">이 값을 늘리면 서버에서 백 압력을 대기 하지 않고 더 큰 메시지를 더 빨리 버퍼링 할 수 있지만 메모리 소비가 증가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-164">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="d63a3-165">모든 전송을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-165">All Transports are enabled.</span></span> | <span data-ttu-id="d63a3-166">클라이언트에서 연결 하는 데 사용할 수 있는 전송을 제한할 수 있는 `HttpTransportType` 값의 비트 플래그 열거형입니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-166">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="d63a3-167">다음을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d63a3-167">See below.</span></span> | <span data-ttu-id="d63a3-168">롱 폴링 전송과 관련된 추가 옵션입니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-168">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="d63a3-169">다음을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d63a3-169">See below.</span></span> | <span data-ttu-id="d63a3-170">WebSockets 전송과 관련된 추가 옵션입니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-170">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="d63a3-171">롱 폴링 전송에는 `LongPolling` 속성을 이용해서 구성할 수 있는 추가적인 옵션이 존재합니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-171">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="d63a3-172">옵션</span><span class="sxs-lookup"><span data-stu-id="d63a3-172">Option</span></span> | <span data-ttu-id="d63a3-173">기본값</span><span class="sxs-lookup"><span data-stu-id="d63a3-173">Default Value</span></span> | <span data-ttu-id="d63a3-174">설명</span><span class="sxs-lookup"><span data-stu-id="d63a3-174">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="d63a3-175">90초</span><span class="sxs-lookup"><span data-stu-id="d63a3-175">90 seconds</span></span> | <span data-ttu-id="d63a3-176">단일 폴링 요청을 종료하기 전에 서버가 메시지를 클라이언트에 전송할 때까지 대기하는 최대 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-176">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="d63a3-177">이 값을 줄이면 클라이언트는 더 자주 새 폴링 요청을 발행합니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-177">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="d63a3-178">WebSocket 전송에는 `WebSockets` 속성을 이용해서 구성할 수 있는 추가적인 옵션이 존재합니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-178">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="d63a3-179">옵션</span><span class="sxs-lookup"><span data-stu-id="d63a3-179">Option</span></span> | <span data-ttu-id="d63a3-180">기본값</span><span class="sxs-lookup"><span data-stu-id="d63a3-180">Default Value</span></span> | <span data-ttu-id="d63a3-181">설명</span><span class="sxs-lookup"><span data-stu-id="d63a3-181">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="d63a3-182">5초</span><span class="sxs-lookup"><span data-stu-id="d63a3-182">5 seconds</span></span> | <span data-ttu-id="d63a3-183">서버가 닫힌 후 클라이언트가 이 시간 제한 내에 닫기에 실패하면 연결이 종료됩니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-183">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="d63a3-184">사용자 지정 값으로 `Sec-WebSocket-Protocol` 헤더를 설정하기 위해 사용할 수 있는 대리자입니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-184">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="d63a3-185">이 대리자는 클라이언트에서 요청한 값을 입력으로 받아서 원하는 값을 반환해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-185">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="d63a3-186">클라이언트 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="d63a3-186">Configure client options</span></span>

<span data-ttu-id="d63a3-187">클라이언트 옵션은 `HubConnectionBuilder` 형식 (.NET 및 JavaScript 클라이언트에서 사용 가능)에서 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-187">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="d63a3-188">Java 클라이언트 에서도 사용할 수 있지만 `HttpHubConnectionBuilder` 하위 클래스는 작성기 구성 옵션과 `HubConnection` 자체를 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-188">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="d63a3-189">로깅 구성</span><span class="sxs-lookup"><span data-stu-id="d63a3-189">Configure logging</span></span>

<span data-ttu-id="d63a3-190">.NET 클라이언트에서 로깅은 `ConfigureLogging` 메서드를 이용해서 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-190">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="d63a3-191">로깅 공급자 및 필터는 서버와 동일한 방법으로 등록할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-191">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="d63a3-192">자세한 내용은 [ASP.NET Core 로깅](xref:fundamentals/logging/index) 설명서를 참고하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-192">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="d63a3-193">로깅 공급자를 등록하는 데 필요한 패키지를 설치해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-193">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="d63a3-194">전체 목록은 로깅 문서의 [기본 제공 로깅 공급자](xref:fundamentals/logging/index#built-in-logging-providers) 섹션을 참고하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-194">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="d63a3-195">예를 들어 콘솔 로깅을 사용하려면 `Microsoft.Extensions.Logging.Console` NuGet 패키지를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-195">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="d63a3-196">`AddConsole` 확장 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-196">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="d63a3-197">JavaScript 클라이언트에는 유사한 `configureLogging` 메서드가 존재합니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-197">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="d63a3-198">기록할 로그 메시지의 최소 수준을 나타내는 `LogLevel` 값을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-198">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="d63a3-199">로그는 브라우저 콘솔 창에 기록됩니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-199">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="d63a3-200">`LogLevel` 값 대신 로그 수준 이름을 나타내는 `string` 값을 제공할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-200">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="d63a3-201">이는 `LogLevel` 상수에 대 한 액세스 권한이 없는 환경에서 SignalR 로깅을 구성할 때 유용 합니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-201">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="d63a3-202">다음 표에서는 사용 가능한 로그 수준을 나열 합니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-202">The following table lists the available log levels.</span></span> <span data-ttu-id="d63a3-203">사용자가 `configureLogging`에 제공 하는 값은 로깅할 **최소** 로그 수준을 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-203">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="d63a3-204">이 수준에서 기록 되 **는 메시지 또는 테이블의 이후 나열 된 수준**에 기록 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-204">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="d63a3-205">문자열</span><span class="sxs-lookup"><span data-stu-id="d63a3-205">String</span></span>                      | <span data-ttu-id="d63a3-206">LogLevel</span><span class="sxs-lookup"><span data-stu-id="d63a3-206">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="d63a3-207">`info` **or** `information`</span><span class="sxs-lookup"><span data-stu-id="d63a3-207">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="d63a3-208">`warn` **or** `warning`</span><span class="sxs-lookup"><span data-stu-id="d63a3-208">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="d63a3-209">로깅을 완전히 비활성화시키려면 `configureLogging` 메서드에서 `signalR.LogLevel.None`을 지정하십시오.</span><span class="sxs-lookup"><span data-stu-id="d63a3-209">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="d63a3-210">로깅에 대 한 자세한 내용은 [SignalR 진단 설명서](xref:signalr/diagnostics)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="d63a3-210">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="d63a3-211">SignalR Java 클라이언트는 [SLF4J](https://www.slf4j.org/) 라이브러리를 사용 하 여 로깅을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-211">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="d63a3-212">이는 라이브러리의 사용자가 특정 로깅 종속성을 가져와 자신의 특정 로깅 구현을 선택할 수 있게 해 주는 상위 수준 로깅 API입니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-212">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="d63a3-213">다음 코드 조각에서는 SignalR Java 클라이언트와 함께 `java.util.logging`를 사용 하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-213">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="d63a3-214">종속성에서 로깅을 구성 하지 않으면 SLF4J는 다음과 같은 경고 메시지와 함께 기본 작업 없음로 거를 로드 합니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-214">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="d63a3-215">이는 무시 해도 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-215">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="d63a3-216">허용되는 전송 구성</span><span class="sxs-lookup"><span data-stu-id="d63a3-216">Configure allowed transports</span></span>

<span data-ttu-id="d63a3-217">SignalR에서 사용 하는 전송은 `WithUrl` 호출 (JavaScript의`withUrl`)에서 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-217">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="d63a3-218">`HttpTransportType` 값들의 비트 OR을 이용해서 클라이언트가 지정한 전송만 사용하도록 제한할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-218">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="d63a3-219">기본적으로 모든 전송은 활성화되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-219">All transports are enabled by default.</span></span>

<span data-ttu-id="d63a3-220">예를 들어 다음 코드는 서버-전송 이벤트 전송을 비활성화시키고 WebSocket 및 롱 폴링 연결만 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-220">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="d63a3-221">JavaScript 클라이언트에서 전송은 `withUrl`에 제공되는 옵션 개체의 `transport` 필드를 설정하여 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-221">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="d63a3-222">이 버전의 Java 클라이언트 websocket은 유일 하 게 사용할 수 있는 전송입니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-222">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="d63a3-223">Java 클라이언트에서 `HttpHubConnectionBuilder`의 `withTransport` 메서드를 사용 하 여 전송이 선택 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-223">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="d63a3-224">Java 클라이언트는 기본적으로 Websocket 전송을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-224">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="d63a3-225">SignalR Java 클라이언트는 아직 전송 대체를 지원 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-225">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="d63a3-226">전달자 인증 구성</span><span class="sxs-lookup"><span data-stu-id="d63a3-226">Configure bearer authentication</span></span>

<span data-ttu-id="d63a3-227">SignalR 요청과 함께 인증 데이터를 제공 하려면 JavaScript에서`accessTokenFactory` `AccessTokenProvider` 옵션을 사용 하 여 원하는 액세스 토큰을 반환 하는 함수를 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-227">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="d63a3-228">.NET 클라이언트에서는 액세스 토큰이 HTTP "Bearer Authentication" 토큰으로 전달됩니다(`Bearer` 형식의 `Authorization` 헤더를 통해).</span><span class="sxs-lookup"><span data-stu-id="d63a3-228">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="d63a3-229">JavaScript 클라이언트에서는 브라우저의 API가 헤더 적용 기능을 제한하는 몇 가지 경우를 **제외**하면 (특히 서버-전송 이벤트 및 WebSockets 요청) 액세스 토큰이 전달자 토큰으로 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-229">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="d63a3-230">이런 경우 액세스 토큰은 `access_token`이라는 쿼리 문자열 값으로 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-230">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="d63a3-231">.NET 클라이언트에서 `AccessTokenProvider` 옵션은 `WithUrl`에서 옵션 대리자를 이용해서 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-231">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="d63a3-232">JavaScript 클라이언트에서는 `withUrl`에서 옵션 개체의 `accessTokenFactory` 필드를 설정하여 액세스 토큰을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-232">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

<span data-ttu-id="d63a3-233">SignalR Java 클라이언트에서 [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)에 액세스 토큰 팩터리를 제공 하 여 인증에 사용할 전달자 토큰을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-233">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="d63a3-234">[WithAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) 를 사용 하 여 [rxjava](https://github.com/ReactiveX/RxJava) [Single\<문자열 >](https://reactivex.io/documentation/single.html)를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-234">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="d63a3-235">[단일. 지연](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)에 대 한 호출을 사용 하 여 클라이언트에 대 한 액세스 토큰을 생성 하는 논리를 작성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-235">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="d63a3-236">시간 제한 및 연결 유지 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="d63a3-236">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="d63a3-237">시간 제한 및 연결 유지 동작을 구성하기 위한 추가 옵션은 `HubConnection` 개체 자체에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-237">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="nettabdotnet"></a>[<span data-ttu-id="d63a3-238">.NET</span><span class="sxs-lookup"><span data-stu-id="d63a3-238">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="d63a3-239">옵션</span><span class="sxs-lookup"><span data-stu-id="d63a3-239">Option</span></span> | <span data-ttu-id="d63a3-240">기본값</span><span class="sxs-lookup"><span data-stu-id="d63a3-240">Default value</span></span> | <span data-ttu-id="d63a3-241">설명</span><span class="sxs-lookup"><span data-stu-id="d63a3-241">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="d63a3-242">30초(30000밀리초)</span><span class="sxs-lookup"><span data-stu-id="d63a3-242">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="d63a3-243">서버 활동 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-243">Timeout for server activity.</span></span> <span data-ttu-id="d63a3-244">서버가 이 시간 제한 내에 메시지를 전송하지 않으면 클라이언트는 서버 연결이 끊어졌다고 간주하고 `Closed` 이벤트(JavaScript에서는 `onclose` 이벤트)를  트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-244">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="d63a3-245">이 값이 충분히 커야만 ping 메시지가 서버에서 전송**되고** 시간 제한 내에 클라이언트에 의해 수신될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-245">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="d63a3-246">권장 값은 ping이 도착할 때까지 시간을 허용 하기 위해 서버의 `KeepAliveInterval` 값을 두 번 이상 사용 하는 숫자입니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-246">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="d63a3-247">15초</span><span class="sxs-lookup"><span data-stu-id="d63a3-247">15 seconds</span></span> | <span data-ttu-id="d63a3-248">초기 서버 핸드셰이크에 대한 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-248">Timeout for initial server handshake.</span></span> <span data-ttu-id="d63a3-249">서버가 이 시간 제한 내에 핸드셰이크 응답을 전송하지 않으면 클라이언트는 핸드셰이크를 취소하고 `Closed` 이벤트(JavaScript에서는 `onclose` 이벤트)를 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-249">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="d63a3-250">이 설정은 심각한 네트워크 지연으로 인해 핸드셰이크 시간 제한 오류가 발생하는 경우에만 수정해야 하는 고급 설정입니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-250">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="d63a3-251">핸드셰이크 프로세스에 대 한 자세한 내용은 [SignalR 허브 프로토콜 사양](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="d63a3-251">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="d63a3-252">15초</span><span class="sxs-lookup"><span data-stu-id="d63a3-252">15 seconds</span></span> | <span data-ttu-id="d63a3-253">클라이언트에서 ping 메시지를 보내는 간격을 결정 합니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-253">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="d63a3-254">클라이언트에서 메시지를 보내면 타이머를 간격의 시작으로 다시 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-254">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="d63a3-255">클라이언트가 서버에 설정 된 `ClientTimeoutInterval`에서 메시지를 보내지 않은 경우 서버는 클라이언트의 연결을 끊은 것으로 간주 합니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-255">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="d63a3-256">.NET 클라이언트에서 시간 제한 값은 `TimeSpan` 값으로 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-256">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascripttabjavascript"></a>[<span data-ttu-id="d63a3-257">JavaScript</span><span class="sxs-lookup"><span data-stu-id="d63a3-257">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="d63a3-258">옵션</span><span class="sxs-lookup"><span data-stu-id="d63a3-258">Option</span></span> | <span data-ttu-id="d63a3-259">기본값</span><span class="sxs-lookup"><span data-stu-id="d63a3-259">Default value</span></span> | <span data-ttu-id="d63a3-260">설명</span><span class="sxs-lookup"><span data-stu-id="d63a3-260">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="d63a3-261">30초(30000밀리초)</span><span class="sxs-lookup"><span data-stu-id="d63a3-261">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="d63a3-262">서버 활동 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-262">Timeout for server activity.</span></span> <span data-ttu-id="d63a3-263">서버가이 간격 내에 메시지를 보내지 않은 경우 클라이언트는 서버 연결을 끊고 `onclose` 이벤트를 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-263">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="d63a3-264">이 값이 충분히 커야만 ping 메시지가 서버에서 전송**되고** 시간 제한 내에 클라이언트에 의해 수신될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-264">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="d63a3-265">권장 값은 ping이 도착할 때까지 시간을 허용 하기 위해 서버의 `KeepAliveInterval` 값을 두 번 이상 사용 하는 숫자입니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-265">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="d63a3-266">15 초 (15000 밀리초)</span><span class="sxs-lookup"><span data-stu-id="d63a3-266">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="d63a3-267">클라이언트에서 ping 메시지를 보내는 간격을 결정 합니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-267">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="d63a3-268">클라이언트에서 메시지를 보내면 타이머를 간격의 시작으로 다시 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-268">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="d63a3-269">클라이언트가 서버에 설정 된 `ClientTimeoutInterval`에서 메시지를 보내지 않은 경우 서버는 클라이언트의 연결을 끊은 것으로 간주 합니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-269">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="javatabjava"></a>[<span data-ttu-id="d63a3-270">Java</span><span class="sxs-lookup"><span data-stu-id="d63a3-270">Java</span></span>](#tab/java)

| <span data-ttu-id="d63a3-271">옵션</span><span class="sxs-lookup"><span data-stu-id="d63a3-271">Option</span></span> | <span data-ttu-id="d63a3-272">기본값</span><span class="sxs-lookup"><span data-stu-id="d63a3-272">Default value</span></span> | <span data-ttu-id="d63a3-273">설명</span><span class="sxs-lookup"><span data-stu-id="d63a3-273">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="d63a3-274">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="d63a3-274">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="d63a3-275">30초(30000밀리초)</span><span class="sxs-lookup"><span data-stu-id="d63a3-275">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="d63a3-276">서버 활동 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-276">Timeout for server activity.</span></span> <span data-ttu-id="d63a3-277">서버가이 간격 내에 메시지를 보내지 않은 경우 클라이언트는 서버 연결을 끊고 `onClose` 이벤트를 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-277">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="d63a3-278">이 값이 충분히 커야만 ping 메시지가 서버에서 전송**되고** 시간 제한 내에 클라이언트에 의해 수신될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-278">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="d63a3-279">권장 값은 ping이 도착할 때까지 시간을 허용 하기 위해 서버의 `KeepAliveInterval` 값을 두 번 이상 사용 하는 숫자입니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-279">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="d63a3-280">15초</span><span class="sxs-lookup"><span data-stu-id="d63a3-280">15 seconds</span></span> | <span data-ttu-id="d63a3-281">초기 서버 핸드셰이크에 대한 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-281">Timeout for initial server handshake.</span></span> <span data-ttu-id="d63a3-282">서버가이 간격 내에 핸드셰이크 응답을 보내지 않는 경우 클라이언트는 핸드셰이크를 취소 하 고 `onClose` 이벤트를 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-282">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="d63a3-283">이 설정은 심각한 네트워크 지연으로 인해 핸드셰이크 시간 제한 오류가 발생하는 경우에만 수정해야 하는 고급 설정입니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-283">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="d63a3-284">핸드셰이크 프로세스에 대 한 자세한 내용은 [SignalR 허브 프로토콜 사양](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="d63a3-284">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="d63a3-285">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="d63a3-285">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="d63a3-286">15 초 (15000 밀리초)</span><span class="sxs-lookup"><span data-stu-id="d63a3-286">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="d63a3-287">클라이언트에서 ping 메시지를 보내는 간격을 결정 합니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-287">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="d63a3-288">클라이언트에서 메시지를 보내면 타이머를 간격의 시작으로 다시 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-288">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="d63a3-289">클라이언트가 서버에 설정 된 `ClientTimeoutInterval`에서 메시지를 보내지 않은 경우 서버는 클라이언트의 연결을 끊은 것으로 간주 합니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-289">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="d63a3-290">추가 구성 옵션</span><span class="sxs-lookup"><span data-stu-id="d63a3-290">Configure additional options</span></span>

<span data-ttu-id="d63a3-291">추가 옵션은 Java 클라이언트의 `HttpHubConnectionBuilder`에서 `HubConnectionBuilder` 또는 다양 한 구성 Api에 대 한 `WithUrl` (JavaScript의`withUrl`) 방법으로 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-291">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="nettabdotnet"></a>[<span data-ttu-id="d63a3-292">.NET</span><span class="sxs-lookup"><span data-stu-id="d63a3-292">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="d63a3-293">.NET 옵션</span><span class="sxs-lookup"><span data-stu-id="d63a3-293">.NET Option</span></span> |  <span data-ttu-id="d63a3-294">기본값</span><span class="sxs-lookup"><span data-stu-id="d63a3-294">Default value</span></span> | <span data-ttu-id="d63a3-295">설명</span><span class="sxs-lookup"><span data-stu-id="d63a3-295">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="d63a3-296">HTTP 요청에서 전달자 인증 토큰으로 제공된 문자열을 반환하는 함수입니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-296">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="d63a3-297">이 값을 `true`로 설정하면 협상 단계를 건너뜁니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-297">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="d63a3-298">**WebSockets 전송이 유일하게 활성화된 전송인 경우에만 지원됩니다.**</span><span class="sxs-lookup"><span data-stu-id="d63a3-298">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="d63a3-299">Azure SignalR 서비스를 사용 하는 경우이 설정을 사용 하도록 설정할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-299">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="d63a3-300">비어 있음</span><span class="sxs-lookup"><span data-stu-id="d63a3-300">Empty</span></span> | <span data-ttu-id="d63a3-301">요청을 인증하기 위해 전송할 TLS 인증서의 컬렉션입니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-301">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="d63a3-302">비어 있음</span><span class="sxs-lookup"><span data-stu-id="d63a3-302">Empty</span></span> | <span data-ttu-id="d63a3-303">모든 HTTP 요청과 함께 전송할 HTTP 쿠키의 컬렉션입니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-303">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="d63a3-304">비어 있음</span><span class="sxs-lookup"><span data-stu-id="d63a3-304">Empty</span></span> | <span data-ttu-id="d63a3-305">모든 HTTP 요청과 함께 전송할 자격 증명입니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-305">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="d63a3-306">5초</span><span class="sxs-lookup"><span data-stu-id="d63a3-306">5 seconds</span></span> | <span data-ttu-id="d63a3-307">Websocket에만 해당됩니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-307">WebSockets only.</span></span> <span data-ttu-id="d63a3-308">클라이언트가 서버를 닫은 후 닫기 요청을 확인하기 위해 대기하는 최대 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-308">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="d63a3-309">서버가 이 시간 내에 종료를 승인하지 않으면 클라이언트가 연결을 끊습니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-309">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="d63a3-310">비어 있음</span><span class="sxs-lookup"><span data-stu-id="d63a3-310">Empty</span></span> | <span data-ttu-id="d63a3-311">모든 HTTP 요청과 함께 보낼 추가 HTTP 헤더의 맵입니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-311">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="d63a3-312">HTTP 요청을 전송하기 위해 사용되는 `HttpMessageHandler`를 구성하거나 대체하는 데 사용할 수 있는 대리자입니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-312">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="d63a3-313">WebSocket 연결에는 사용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-313">Not used for WebSocket connections.</span></span> <span data-ttu-id="d63a3-314">이 대리자는 null이 아닌 값을 반환해야 하며 매개 변수로 기본값을 받습니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-314">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="d63a3-315">이 기본값의 설정을 수정하여 반환하거나 새로운 `HttpMessageHandler` 인스턴스를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-315">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="d63a3-316">**처리기를 대체할 때 제공 된 처리기에서 유지할 설정을 복사 해야 합니다. 그렇지 않으면 구성 된 옵션 (예: 쿠키 및 헤더)이 새 처리기에 적용 되지 않습니다.**</span><span class="sxs-lookup"><span data-stu-id="d63a3-316">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="d63a3-317">HTTP 요청을 전송할 때 사용할 HTTP 프록시입니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-317">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="d63a3-318">HTTP 및 WebSockets 요청에 대한 기본 자격 증명을 전송하려면 이 부울 값을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-318">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="d63a3-319">그러면 Windows 인증을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-319">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="d63a3-320">추가적인 WebSocket 옵션을 구성할 수 있는 대리자입니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-320">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="d63a3-321">옵션을 구성에 사용할 수 있는 [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions)의 인스턴스를 전달받습니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-321">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascripttabjavascript"></a>[<span data-ttu-id="d63a3-322">JavaScript</span><span class="sxs-lookup"><span data-stu-id="d63a3-322">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="d63a3-323">JavaScript 옵션</span><span class="sxs-lookup"><span data-stu-id="d63a3-323">JavaScript Option</span></span> | <span data-ttu-id="d63a3-324">기본값</span><span class="sxs-lookup"><span data-stu-id="d63a3-324">Default Value</span></span> | <span data-ttu-id="d63a3-325">설명</span><span class="sxs-lookup"><span data-stu-id="d63a3-325">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="d63a3-326">HTTP 요청에서 전달자 인증 토큰으로 제공된 문자열을 반환하는 함수입니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-326">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="d63a3-327">이 값을 `true`로 설정하면 협상 단계를 건너뜁니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-327">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="d63a3-328">**WebSockets 전송이 유일하게 활성화된 전송인 경우에만 지원됩니다.**</span><span class="sxs-lookup"><span data-stu-id="d63a3-328">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="d63a3-329">Azure SignalR 서비스를 사용 하는 경우이 설정을 사용 하도록 설정할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-329">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="javatabjava"></a>[<span data-ttu-id="d63a3-330">Java</span><span class="sxs-lookup"><span data-stu-id="d63a3-330">Java</span></span>](#tab/java)

| <span data-ttu-id="d63a3-331">Java 옵션</span><span class="sxs-lookup"><span data-stu-id="d63a3-331">Java Option</span></span> | <span data-ttu-id="d63a3-332">기본값</span><span class="sxs-lookup"><span data-stu-id="d63a3-332">Default Value</span></span> | <span data-ttu-id="d63a3-333">설명</span><span class="sxs-lookup"><span data-stu-id="d63a3-333">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="d63a3-334">HTTP 요청에서 전달자 인증 토큰으로 제공된 문자열을 반환하는 함수입니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-334">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="d63a3-335">이 값을 `true`로 설정하면 협상 단계를 건너뜁니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-335">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="d63a3-336">**WebSockets 전송이 유일하게 활성화된 전송인 경우에만 지원됩니다.**</span><span class="sxs-lookup"><span data-stu-id="d63a3-336">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="d63a3-337">Azure SignalR 서비스를 사용 하는 경우이 설정을 사용 하도록 설정할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-337">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="d63a3-338">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="d63a3-338">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="d63a3-339">비어 있음</span><span class="sxs-lookup"><span data-stu-id="d63a3-339">Empty</span></span> | <span data-ttu-id="d63a3-340">모든 HTTP 요청과 함께 보낼 추가 HTTP 헤더의 맵입니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-340">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="d63a3-341">.NET 클라이언트에서 이 옵션들은 `WithUrl`에 제공되는 옵션 대리자를 이용해서 수정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-341">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="d63a3-342">JavaScript 클라이언트에서 이 옵션들은 `withUrl`에 제공되는 JavaScript 개체를 통해서 제공될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-342">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="d63a3-343">Java 클라이언트에서 이러한 옵션은에서 반환 되는 `HttpHubConnectionBuilder`에 대 한 메서드로 구성할 수 있습니다 `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="d63a3-343">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="d63a3-344">추가 자료</span><span class="sxs-lookup"><span data-stu-id="d63a3-344">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="d63a3-345">JSON/MessagePack serialization 옵션</span><span class="sxs-lookup"><span data-stu-id="d63a3-345">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="d63a3-346">ASP.NET Core SignalR는 메시지 인코딩에 [JSON](https://www.json.org/) 및 [MessagePack](https://msgpack.org/index.html)의 두 가지 프로토콜을 지원 합니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-346">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="d63a3-347">각 프로토콜에는 serialization 구성 옵션이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-347">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="d63a3-348">JSON serialization을 사용 하 여 서버에 구성할 수 있습니다 합니다 [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) 후 추가할 수 있는 확장 메서드 [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) 에서 프로그램 `Startup.ConfigureServices` 메서드.</span><span class="sxs-lookup"><span data-stu-id="d63a3-348">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="d63a3-349">`AddJsonProtocol` 메서드는 `options` 개체를 전달받는 대리자를 받습니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-349">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="d63a3-350">이 개체의 [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) 속성은 JSON.NET의 `JsonSerializerSettings` 개체로, 인수 및 반환 값의 직렬화를 구성하는 데 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-350">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="d63a3-351">자세한 내용은 [JSON.NET 설명서](https://www.newtonsoft.com/json/help/html/Introduction.htm)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="d63a3-351">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="d63a3-352">예를 들어 기본 "camelCase" 이름 대신 "대/소문자 구분" 속성 이름을 사용 하도록 serializer를 구성 하려면 `Startup.ConfigureServices`에서 다음 코드를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-352">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="d63a3-353">.NET 클라이언트에서는 [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)에 동일한 `AddJsonProtocol` 확장 메서드가 존재합니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-353">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="d63a3-354">이 확장 메서드를 확인하려면 `Microsoft.Extensions.DependencyInjection` 네임스페이스를 임포트해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-354">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

```csharp
// At the top of the file:
using Microsoft.Extensions.DependencyInjection;
 
// When constructing your connection:
var connection = new HubConnectionBuilder()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    })
    .Build();
```

> [!NOTE]
> <span data-ttu-id="d63a3-355">현재 JavaScript 클라이언트에서는 JSON 직렬화를 구성할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-355">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="d63a3-356">MessagePack serialization 옵션</span><span class="sxs-lookup"><span data-stu-id="d63a3-356">MessagePack serialization options</span></span>

<span data-ttu-id="d63a3-357">MessagePack 직렬화는 [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) 호출에 대리자를 제공하여 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-357">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="d63a3-358">자세한 내용은 [SignalR의 MessagePack를](xref:signalr/messagepackhubprotocol) 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="d63a3-358">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="d63a3-359">현재 JavaScript 클라이언트에서는 MessagePack 직렬화를 구성할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-359">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="d63a3-360">서버 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="d63a3-360">Configure server options</span></span>

<span data-ttu-id="d63a3-361">다음 표에서 SignalR 허브를 구성 하는 옵션을 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-361">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="d63a3-362">옵션</span><span class="sxs-lookup"><span data-stu-id="d63a3-362">Option</span></span> | <span data-ttu-id="d63a3-363">기본값</span><span class="sxs-lookup"><span data-stu-id="d63a3-363">Default Value</span></span> | <span data-ttu-id="d63a3-364">설명</span><span class="sxs-lookup"><span data-stu-id="d63a3-364">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="d63a3-365">30초</span><span class="sxs-lookup"><span data-stu-id="d63a3-365">30 seconds</span></span> | <span data-ttu-id="d63a3-366">서버는이 간격 내에 메시지를 받지 못한 경우 (keep-alive 포함) 클라이언트의 연결을 해제 하는 것으로 간주 합니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-366">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="d63a3-367">이를 구현 하는 방법으로 인해 클라이언트에서 실제로 연결이 끊어진 것으로 표시 되는 시간 제한 간격 보다 오래 걸릴 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-367">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="d63a3-368">권장 값은 두 개의 `KeepAliveInterval` 값입니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-368">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="d63a3-369">15초</span><span class="sxs-lookup"><span data-stu-id="d63a3-369">15 seconds</span></span> | <span data-ttu-id="d63a3-370">클라이언트가 이 시간 제한 내에 초기 핸드셰이크 메시지를 전송하지 않으면 연결이 닫힙니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-370">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="d63a3-371">이 설정은 심각한 네트워크 지연으로 인해 핸드셰이크 시간 제한 오류가 발생하는 경우에만 수정해야 하는 고급 설정입니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-371">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="d63a3-372">핸드셰이크 프로세스에 대 한 자세한 내용은 [SignalR 허브 프로토콜 사양](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="d63a3-372">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="d63a3-373">15초</span><span class="sxs-lookup"><span data-stu-id="d63a3-373">15 seconds</span></span> | <span data-ttu-id="d63a3-374">서버가 이 간격 내에 메시지를 전송하지 않으면 자동으로 ping 메시지가 전송되어 연결이 열린 상태로 유지됩니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-374">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="d63a3-375">`KeepAliveInterval`을 변경할 경우 클라이언트의 `ServerTimeout`/`serverTimeoutInMilliseconds` 설정도 변경하십시오.</span><span class="sxs-lookup"><span data-stu-id="d63a3-375">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="d63a3-376">권장되는 `ServerTimeout`/`serverTimeoutInMilliseconds` 값은 `KeepAliveInterval` 값의 두 배입니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-376">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="d63a3-377">설치된 모든 프로토콜</span><span class="sxs-lookup"><span data-stu-id="d63a3-377">All installed protocols</span></span> | <span data-ttu-id="d63a3-378">허브가 지원하는 프로토콜입니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-378">Protocols supported by this hub.</span></span> <span data-ttu-id="d63a3-379">기본적으로 서버에 등록된 모든 프로토콜이 허용되지만 이 목록에서 프로토콜을 제거하여 개별 허브에 대해 특정 프로토콜을 비활성화시킬 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-379">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="d63a3-380">이 값이 `true`면 Hub 메서드에서 예외가 발생할 경우 자세한 예외 메시지가 클라이언트로 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-380">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="d63a3-381">예외 메시지에는 민감한 정보가 포함되어 있을 수 있으므로 기본값은 `false`입니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-381">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="d63a3-382">`Startup.ConfigureServices`에서 `AddSignalR` 호출에 옵션 대리자를 제공하여 모든 허브에 대한 옵션을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-382">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSignalR(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

<span data-ttu-id="d63a3-383">단일 허브에 대 한 옵션은 `AddSignalR`에서 제공 하는 전역 옵션을 재정의 하며 <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>를 사용 하 여 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-383">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="d63a3-384">고급 HTTP 구성 옵션</span><span class="sxs-lookup"><span data-stu-id="d63a3-384">Advanced HTTP configuration options</span></span>

<span data-ttu-id="d63a3-385">전송 및 메모리 버퍼 관리와 관련된 고급 설정을 구성하려면 `HttpConnectionDispatcherOptions`를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-385">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="d63a3-386">이러한 옵션은 `Startup.Configure`에서 [\<t >의 Maphub](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) 에 대리자를 전달 하 여 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-386">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseSignalR((configure) =>
    {
        var desiredTransports =
            HttpTransportType.WebSockets |
            HttpTransportType.LongPolling;

        configure.MapHub<MyHub>("/myhub", (options) =>
        {
            options.Transports = desiredTransports;
        });
    });
}
```

<span data-ttu-id="d63a3-387">다음 표에서는 ASP.NET Core SignalR의 고급 HTTP 옵션을 구성 하는 옵션을 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-387">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="d63a3-388">옵션</span><span class="sxs-lookup"><span data-stu-id="d63a3-388">Option</span></span> | <span data-ttu-id="d63a3-389">기본값</span><span class="sxs-lookup"><span data-stu-id="d63a3-389">Default Value</span></span> | <span data-ttu-id="d63a3-390">설명</span><span class="sxs-lookup"><span data-stu-id="d63a3-390">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="d63a3-391">32KB</span><span class="sxs-lookup"><span data-stu-id="d63a3-391">32 KB</span></span> | <span data-ttu-id="d63a3-392">클라이언트로부터 수신하는 서버 버퍼의 최대 바이트 수입니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-392">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="d63a3-393">이 값을 늘리면 서버가 더 큰 메시지를 수신할 수 있지만 메모리 소비에 부정적인 영향을 줄 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-393">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="d63a3-394">허브 클래스에 적용된 `Authorize` 특성에서 자동으로 수집된 데이터입니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-394">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="d63a3-395">클라이언트가 허브에 연결할 권한이 있는지 확인하는 데 사용되는 [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) 개체의 목록입니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-395">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="d63a3-396">32KB</span><span class="sxs-lookup"><span data-stu-id="d63a3-396">32 KB</span></span> | <span data-ttu-id="d63a3-397">앱에서 전송하는 서버 버퍼의 최대 바이트 수입니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-397">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="d63a3-398">이 값을 늘리면 서버가 더 큰 메시지를 전송할 수 있지만 메모리 소비에 부정적인 영향을 줄 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-398">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="d63a3-399">모든 전송을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-399">All Transports are enabled.</span></span> | <span data-ttu-id="d63a3-400">클라이언트에서 연결 하는 데 사용할 수 있는 전송을 제한할 수 있는 `HttpTransportType` 값의 비트 플래그 열거형입니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-400">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="d63a3-401">다음을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d63a3-401">See below.</span></span> | <span data-ttu-id="d63a3-402">롱 폴링 전송과 관련된 추가 옵션입니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-402">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="d63a3-403">다음을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d63a3-403">See below.</span></span> | <span data-ttu-id="d63a3-404">WebSockets 전송과 관련된 추가 옵션입니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-404">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="d63a3-405">롱 폴링 전송에는 `LongPolling` 속성을 이용해서 구성할 수 있는 추가적인 옵션이 존재합니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-405">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="d63a3-406">옵션</span><span class="sxs-lookup"><span data-stu-id="d63a3-406">Option</span></span> | <span data-ttu-id="d63a3-407">기본값</span><span class="sxs-lookup"><span data-stu-id="d63a3-407">Default Value</span></span> | <span data-ttu-id="d63a3-408">설명</span><span class="sxs-lookup"><span data-stu-id="d63a3-408">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="d63a3-409">90초</span><span class="sxs-lookup"><span data-stu-id="d63a3-409">90 seconds</span></span> | <span data-ttu-id="d63a3-410">단일 폴링 요청을 종료하기 전에 서버가 메시지를 클라이언트에 전송할 때까지 대기하는 최대 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-410">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="d63a3-411">이 값을 줄이면 클라이언트는 더 자주 새 폴링 요청을 발행합니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-411">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="d63a3-412">WebSocket 전송에는 `WebSockets` 속성을 이용해서 구성할 수 있는 추가적인 옵션이 존재합니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-412">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="d63a3-413">옵션</span><span class="sxs-lookup"><span data-stu-id="d63a3-413">Option</span></span> | <span data-ttu-id="d63a3-414">기본값</span><span class="sxs-lookup"><span data-stu-id="d63a3-414">Default Value</span></span> | <span data-ttu-id="d63a3-415">설명</span><span class="sxs-lookup"><span data-stu-id="d63a3-415">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="d63a3-416">5초</span><span class="sxs-lookup"><span data-stu-id="d63a3-416">5 seconds</span></span> | <span data-ttu-id="d63a3-417">서버가 닫힌 후 클라이언트가 이 시간 제한 내에 닫기에 실패하면 연결이 종료됩니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-417">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="d63a3-418">사용자 지정 값으로 `Sec-WebSocket-Protocol` 헤더를 설정하기 위해 사용할 수 있는 대리자입니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-418">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="d63a3-419">이 대리자는 클라이언트에서 요청한 값을 입력으로 받아서 원하는 값을 반환해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-419">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="d63a3-420">클라이언트 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="d63a3-420">Configure client options</span></span>

<span data-ttu-id="d63a3-421">클라이언트 옵션은 `HubConnectionBuilder` 형식 (.NET 및 JavaScript 클라이언트에서 사용 가능)에서 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-421">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="d63a3-422">Java 클라이언트 에서도 사용할 수 있지만 `HttpHubConnectionBuilder` 하위 클래스는 작성기 구성 옵션과 `HubConnection` 자체를 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-422">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="d63a3-423">로깅 구성</span><span class="sxs-lookup"><span data-stu-id="d63a3-423">Configure logging</span></span>

<span data-ttu-id="d63a3-424">.NET 클라이언트에서 로깅은 `ConfigureLogging` 메서드를 이용해서 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-424">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="d63a3-425">로깅 공급자 및 필터는 서버와 동일한 방법으로 등록할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-425">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="d63a3-426">자세한 내용은 [ASP.NET Core 로깅](xref:fundamentals/logging/index) 설명서를 참고하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-426">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="d63a3-427">로깅 공급자를 등록하는 데 필요한 패키지를 설치해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-427">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="d63a3-428">전체 목록은 로깅 문서의 [기본 제공 로깅 공급자](xref:fundamentals/logging/index#built-in-logging-providers) 섹션을 참고하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-428">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="d63a3-429">예를 들어 콘솔 로깅을 사용하려면 `Microsoft.Extensions.Logging.Console` NuGet 패키지를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-429">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="d63a3-430">`AddConsole` 확장 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-430">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="d63a3-431">JavaScript 클라이언트에는 유사한 `configureLogging` 메서드가 존재합니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-431">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="d63a3-432">기록할 로그 메시지의 최소 수준을 나타내는 `LogLevel` 값을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-432">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="d63a3-433">로그는 브라우저 콘솔 창에 기록됩니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-433">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="d63a3-434">로깅을 완전히 비활성화시키려면 `configureLogging` 메서드에서 `signalR.LogLevel.None`을 지정하십시오.</span><span class="sxs-lookup"><span data-stu-id="d63a3-434">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="d63a3-435">로깅에 대 한 자세한 내용은 [SignalR 진단 설명서](xref:signalr/diagnostics)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="d63a3-435">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="d63a3-436">SignalR Java 클라이언트는 [SLF4J](https://www.slf4j.org/) 라이브러리를 사용 하 여 로깅을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-436">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="d63a3-437">이는 라이브러리의 사용자가 특정 로깅 종속성을 가져와 자신의 특정 로깅 구현을 선택할 수 있게 해 주는 상위 수준 로깅 API입니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-437">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="d63a3-438">다음 코드 조각에서는 SignalR Java 클라이언트와 함께 `java.util.logging`를 사용 하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-438">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="d63a3-439">종속성에서 로깅을 구성 하지 않으면 SLF4J는 다음과 같은 경고 메시지와 함께 기본 작업 없음로 거를 로드 합니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-439">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="d63a3-440">이는 무시 해도 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-440">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="d63a3-441">허용되는 전송 구성</span><span class="sxs-lookup"><span data-stu-id="d63a3-441">Configure allowed transports</span></span>

<span data-ttu-id="d63a3-442">SignalR에서 사용 하는 전송은 `WithUrl` 호출 (JavaScript의`withUrl`)에서 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-442">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="d63a3-443">`HttpTransportType` 값들의 비트 OR을 이용해서 클라이언트가 지정한 전송만 사용하도록 제한할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-443">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="d63a3-444">기본적으로 모든 전송은 활성화되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-444">All transports are enabled by default.</span></span>

<span data-ttu-id="d63a3-445">예를 들어 다음 코드는 서버-전송 이벤트 전송을 비활성화시키고 WebSocket 및 롱 폴링 연결만 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-445">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="d63a3-446">JavaScript 클라이언트에서 전송은 `withUrl`에 제공되는 옵션 개체의 `transport` 필드를 설정하여 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-446">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="d63a3-447">이 버전의 Java 클라이언트 websocket은 유일 하 게 사용할 수 있는 전송입니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-447">In this version of the Java client websockets is the only available transport.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="d63a3-448">전달자 인증 구성</span><span class="sxs-lookup"><span data-stu-id="d63a3-448">Configure bearer authentication</span></span>

<span data-ttu-id="d63a3-449">SignalR 요청과 함께 인증 데이터를 제공 하려면 JavaScript에서`accessTokenFactory` `AccessTokenProvider` 옵션을 사용 하 여 원하는 액세스 토큰을 반환 하는 함수를 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-449">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="d63a3-450">.NET 클라이언트에서는 액세스 토큰이 HTTP "Bearer Authentication" 토큰으로 전달됩니다(`Bearer` 형식의 `Authorization` 헤더를 통해).</span><span class="sxs-lookup"><span data-stu-id="d63a3-450">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="d63a3-451">JavaScript 클라이언트에서는 브라우저의 API가 헤더 적용 기능을 제한하는 몇 가지 경우를 **제외**하면 (특히 서버-전송 이벤트 및 WebSockets 요청) 액세스 토큰이 전달자 토큰으로 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-451">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="d63a3-452">이런 경우 액세스 토큰은 `access_token`이라는 쿼리 문자열 값으로 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-452">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="d63a3-453">.NET 클라이언트에서 `AccessTokenProvider` 옵션은 `WithUrl`에서 옵션 대리자를 이용해서 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-453">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="d63a3-454">JavaScript 클라이언트에서는 `withUrl`에서 옵션 개체의 `accessTokenFactory` 필드를 설정하여 액세스 토큰을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-454">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

<span data-ttu-id="d63a3-455">SignalR Java 클라이언트에서 [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)에 액세스 토큰 팩터리를 제공 하 여 인증에 사용할 전달자 토큰을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-455">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="d63a3-456">[WithAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) 를 사용 하 여 [rxjava](https://github.com/ReactiveX/RxJava) [Single\<문자열 >](https://reactivex.io/documentation/single.html)를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-456">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="d63a3-457">[단일. 지연](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)에 대 한 호출을 사용 하 여 클라이언트에 대 한 액세스 토큰을 생성 하는 논리를 작성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-457">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="d63a3-458">시간 제한 및 연결 유지 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="d63a3-458">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="d63a3-459">시간 제한 및 연결 유지 동작을 구성하기 위한 추가 옵션은 `HubConnection` 개체 자체에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-459">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="nettabdotnet"></a>[<span data-ttu-id="d63a3-460">.NET</span><span class="sxs-lookup"><span data-stu-id="d63a3-460">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="d63a3-461">옵션</span><span class="sxs-lookup"><span data-stu-id="d63a3-461">Option</span></span> | <span data-ttu-id="d63a3-462">기본값</span><span class="sxs-lookup"><span data-stu-id="d63a3-462">Default value</span></span> | <span data-ttu-id="d63a3-463">설명</span><span class="sxs-lookup"><span data-stu-id="d63a3-463">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="d63a3-464">30초(30000밀리초)</span><span class="sxs-lookup"><span data-stu-id="d63a3-464">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="d63a3-465">서버 활동 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-465">Timeout for server activity.</span></span> <span data-ttu-id="d63a3-466">서버가 이 시간 제한 내에 메시지를 전송하지 않으면 클라이언트는 서버 연결이 끊어졌다고 간주하고 `Closed` 이벤트(JavaScript에서는 `onclose` 이벤트)를  트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-466">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="d63a3-467">이 값이 충분히 커야만 ping 메시지가 서버에서 전송**되고** 시간 제한 내에 클라이언트에 의해 수신될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-467">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="d63a3-468">권장 값은 ping이 도착할 때까지 시간을 허용 하기 위해 서버의 `KeepAliveInterval` 값을 두 번 이상 사용 하는 숫자입니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-468">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="d63a3-469">15초</span><span class="sxs-lookup"><span data-stu-id="d63a3-469">15 seconds</span></span> | <span data-ttu-id="d63a3-470">초기 서버 핸드셰이크에 대한 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-470">Timeout for initial server handshake.</span></span> <span data-ttu-id="d63a3-471">서버가 이 시간 제한 내에 핸드셰이크 응답을 전송하지 않으면 클라이언트는 핸드셰이크를 취소하고 `Closed` 이벤트(JavaScript에서는 `onclose` 이벤트)를 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-471">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="d63a3-472">이 설정은 심각한 네트워크 지연으로 인해 핸드셰이크 시간 제한 오류가 발생하는 경우에만 수정해야 하는 고급 설정입니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-472">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="d63a3-473">핸드셰이크 프로세스에 대 한 자세한 내용은 [SignalR 허브 프로토콜 사양](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="d63a3-473">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="d63a3-474">15초</span><span class="sxs-lookup"><span data-stu-id="d63a3-474">15 seconds</span></span> | <span data-ttu-id="d63a3-475">클라이언트에서 ping 메시지를 보내는 간격을 결정 합니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-475">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="d63a3-476">클라이언트에서 메시지를 보내면 타이머를 간격의 시작으로 다시 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-476">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="d63a3-477">클라이언트가 서버에 설정 된 `ClientTimeoutInterval`에서 메시지를 보내지 않은 경우 서버는 클라이언트의 연결을 끊은 것으로 간주 합니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-477">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="d63a3-478">.NET 클라이언트에서 시간 제한 값은 `TimeSpan` 값으로 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-478">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascripttabjavascript"></a>[<span data-ttu-id="d63a3-479">JavaScript</span><span class="sxs-lookup"><span data-stu-id="d63a3-479">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="d63a3-480">옵션</span><span class="sxs-lookup"><span data-stu-id="d63a3-480">Option</span></span> | <span data-ttu-id="d63a3-481">기본값</span><span class="sxs-lookup"><span data-stu-id="d63a3-481">Default value</span></span> | <span data-ttu-id="d63a3-482">설명</span><span class="sxs-lookup"><span data-stu-id="d63a3-482">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="d63a3-483">30초(30000밀리초)</span><span class="sxs-lookup"><span data-stu-id="d63a3-483">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="d63a3-484">서버 활동 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-484">Timeout for server activity.</span></span> <span data-ttu-id="d63a3-485">서버가이 간격 내에 메시지를 보내지 않은 경우 클라이언트는 서버 연결을 끊고 `onclose` 이벤트를 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-485">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="d63a3-486">이 값이 충분히 커야만 ping 메시지가 서버에서 전송**되고** 시간 제한 내에 클라이언트에 의해 수신될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-486">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="d63a3-487">권장 값은 ping이 도착할 때까지 시간을 허용 하기 위해 서버의 `KeepAliveInterval` 값을 두 번 이상 사용 하는 숫자입니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-487">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="d63a3-488">15 초 (15000 밀리초)</span><span class="sxs-lookup"><span data-stu-id="d63a3-488">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="d63a3-489">클라이언트에서 ping 메시지를 보내는 간격을 결정 합니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-489">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="d63a3-490">클라이언트에서 메시지를 보내면 타이머를 간격의 시작으로 다시 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-490">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="d63a3-491">클라이언트가 서버에 설정 된 `ClientTimeoutInterval`에서 메시지를 보내지 않은 경우 서버는 클라이언트의 연결을 끊은 것으로 간주 합니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-491">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="javatabjava"></a>[<span data-ttu-id="d63a3-492">Java</span><span class="sxs-lookup"><span data-stu-id="d63a3-492">Java</span></span>](#tab/java)

| <span data-ttu-id="d63a3-493">옵션</span><span class="sxs-lookup"><span data-stu-id="d63a3-493">Option</span></span> | <span data-ttu-id="d63a3-494">기본값</span><span class="sxs-lookup"><span data-stu-id="d63a3-494">Default value</span></span> | <span data-ttu-id="d63a3-495">설명</span><span class="sxs-lookup"><span data-stu-id="d63a3-495">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="d63a3-496">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="d63a3-496">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="d63a3-497">30초(30000밀리초)</span><span class="sxs-lookup"><span data-stu-id="d63a3-497">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="d63a3-498">서버 활동 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-498">Timeout for server activity.</span></span> <span data-ttu-id="d63a3-499">서버가이 간격 내에 메시지를 보내지 않은 경우 클라이언트는 서버 연결을 끊고 `onClose` 이벤트를 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-499">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="d63a3-500">이 값이 충분히 커야만 ping 메시지가 서버에서 전송**되고** 시간 제한 내에 클라이언트에 의해 수신될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-500">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="d63a3-501">권장 값은 ping이 도착할 때까지 시간을 허용 하기 위해 서버의 `KeepAliveInterval` 값을 두 번 이상 사용 하는 숫자입니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-501">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="d63a3-502">15초</span><span class="sxs-lookup"><span data-stu-id="d63a3-502">15 seconds</span></span> | <span data-ttu-id="d63a3-503">초기 서버 핸드셰이크에 대한 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-503">Timeout for initial server handshake.</span></span> <span data-ttu-id="d63a3-504">서버가이 간격 내에 핸드셰이크 응답을 보내지 않는 경우 클라이언트는 핸드셰이크를 취소 하 고 `onClose` 이벤트를 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-504">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="d63a3-505">이 설정은 심각한 네트워크 지연으로 인해 핸드셰이크 시간 제한 오류가 발생하는 경우에만 수정해야 하는 고급 설정입니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-505">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="d63a3-506">핸드셰이크 프로세스에 대 한 자세한 내용은 [SignalR 허브 프로토콜 사양](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="d63a3-506">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="d63a3-507">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="d63a3-507">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="d63a3-508">15 초 (15000 밀리초)</span><span class="sxs-lookup"><span data-stu-id="d63a3-508">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="d63a3-509">클라이언트에서 ping 메시지를 보내는 간격을 결정 합니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-509">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="d63a3-510">클라이언트에서 메시지를 보내면 타이머를 간격의 시작으로 다시 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-510">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="d63a3-511">클라이언트가 서버에 설정 된 `ClientTimeoutInterval`에서 메시지를 보내지 않은 경우 서버는 클라이언트의 연결을 끊은 것으로 간주 합니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-511">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="d63a3-512">추가 구성 옵션</span><span class="sxs-lookup"><span data-stu-id="d63a3-512">Configure additional options</span></span>

<span data-ttu-id="d63a3-513">추가 옵션은 Java 클라이언트의 `HttpHubConnectionBuilder`에서 `HubConnectionBuilder` 또는 다양 한 구성 Api에 대 한 `WithUrl` (JavaScript의`withUrl`) 방법으로 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-513">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="nettabdotnet"></a>[<span data-ttu-id="d63a3-514">.NET</span><span class="sxs-lookup"><span data-stu-id="d63a3-514">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="d63a3-515">.NET 옵션</span><span class="sxs-lookup"><span data-stu-id="d63a3-515">.NET Option</span></span> |  <span data-ttu-id="d63a3-516">기본값</span><span class="sxs-lookup"><span data-stu-id="d63a3-516">Default value</span></span> | <span data-ttu-id="d63a3-517">설명</span><span class="sxs-lookup"><span data-stu-id="d63a3-517">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="d63a3-518">HTTP 요청에서 전달자 인증 토큰으로 제공된 문자열을 반환하는 함수입니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-518">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="d63a3-519">이 값을 `true`로 설정하면 협상 단계를 건너뜁니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-519">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="d63a3-520">**WebSockets 전송이 유일하게 활성화된 전송인 경우에만 지원됩니다.**</span><span class="sxs-lookup"><span data-stu-id="d63a3-520">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="d63a3-521">Azure SignalR 서비스를 사용 하는 경우이 설정을 사용 하도록 설정할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-521">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="d63a3-522">비어 있음</span><span class="sxs-lookup"><span data-stu-id="d63a3-522">Empty</span></span> | <span data-ttu-id="d63a3-523">요청을 인증하기 위해 전송할 TLS 인증서의 컬렉션입니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-523">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="d63a3-524">비어 있음</span><span class="sxs-lookup"><span data-stu-id="d63a3-524">Empty</span></span> | <span data-ttu-id="d63a3-525">모든 HTTP 요청과 함께 전송할 HTTP 쿠키의 컬렉션입니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-525">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="d63a3-526">비어 있음</span><span class="sxs-lookup"><span data-stu-id="d63a3-526">Empty</span></span> | <span data-ttu-id="d63a3-527">모든 HTTP 요청과 함께 전송할 자격 증명입니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-527">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="d63a3-528">5초</span><span class="sxs-lookup"><span data-stu-id="d63a3-528">5 seconds</span></span> | <span data-ttu-id="d63a3-529">Websocket에만 해당됩니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-529">WebSockets only.</span></span> <span data-ttu-id="d63a3-530">클라이언트가 서버를 닫은 후 닫기 요청을 확인하기 위해 대기하는 최대 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-530">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="d63a3-531">서버가 이 시간 내에 종료를 승인하지 않으면 클라이언트가 연결을 끊습니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-531">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="d63a3-532">비어 있음</span><span class="sxs-lookup"><span data-stu-id="d63a3-532">Empty</span></span> | <span data-ttu-id="d63a3-533">모든 HTTP 요청과 함께 보낼 추가 HTTP 헤더의 맵입니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-533">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="d63a3-534">HTTP 요청을 전송하기 위해 사용되는 `HttpMessageHandler`를 구성하거나 대체하는 데 사용할 수 있는 대리자입니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-534">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="d63a3-535">WebSocket 연결에는 사용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-535">Not used for WebSocket connections.</span></span> <span data-ttu-id="d63a3-536">이 대리자는 null이 아닌 값을 반환해야 하며 매개 변수로 기본값을 받습니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-536">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="d63a3-537">이 기본값의 설정을 수정하여 반환하거나 새로운 `HttpMessageHandler` 인스턴스를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-537">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="d63a3-538">**처리기를 대체할 때 제공 된 처리기에서 유지할 설정을 복사 해야 합니다. 그렇지 않으면 구성 된 옵션 (예: 쿠키 및 헤더)이 새 처리기에 적용 되지 않습니다.**</span><span class="sxs-lookup"><span data-stu-id="d63a3-538">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="d63a3-539">HTTP 요청을 전송할 때 사용할 HTTP 프록시입니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-539">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="d63a3-540">HTTP 및 WebSockets 요청에 대한 기본 자격 증명을 전송하려면 이 부울 값을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-540">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="d63a3-541">그러면 Windows 인증을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-541">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="d63a3-542">추가적인 WebSocket 옵션을 구성할 수 있는 대리자입니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-542">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="d63a3-543">옵션을 구성에 사용할 수 있는 [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions)의 인스턴스를 전달받습니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-543">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascripttabjavascript"></a>[<span data-ttu-id="d63a3-544">JavaScript</span><span class="sxs-lookup"><span data-stu-id="d63a3-544">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="d63a3-545">JavaScript 옵션</span><span class="sxs-lookup"><span data-stu-id="d63a3-545">JavaScript Option</span></span> | <span data-ttu-id="d63a3-546">기본값</span><span class="sxs-lookup"><span data-stu-id="d63a3-546">Default Value</span></span> | <span data-ttu-id="d63a3-547">설명</span><span class="sxs-lookup"><span data-stu-id="d63a3-547">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="d63a3-548">HTTP 요청에서 전달자 인증 토큰으로 제공된 문자열을 반환하는 함수입니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-548">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="d63a3-549">이 값을 `true`로 설정하면 협상 단계를 건너뜁니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-549">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="d63a3-550">**WebSockets 전송이 유일하게 활성화된 전송인 경우에만 지원됩니다.**</span><span class="sxs-lookup"><span data-stu-id="d63a3-550">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="d63a3-551">Azure SignalR 서비스를 사용 하는 경우이 설정을 사용 하도록 설정할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-551">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="javatabjava"></a>[<span data-ttu-id="d63a3-552">Java</span><span class="sxs-lookup"><span data-stu-id="d63a3-552">Java</span></span>](#tab/java)

| <span data-ttu-id="d63a3-553">Java 옵션</span><span class="sxs-lookup"><span data-stu-id="d63a3-553">Java Option</span></span> | <span data-ttu-id="d63a3-554">기본값</span><span class="sxs-lookup"><span data-stu-id="d63a3-554">Default Value</span></span> | <span data-ttu-id="d63a3-555">설명</span><span class="sxs-lookup"><span data-stu-id="d63a3-555">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="d63a3-556">HTTP 요청에서 전달자 인증 토큰으로 제공된 문자열을 반환하는 함수입니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-556">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="d63a3-557">이 값을 `true`로 설정하면 협상 단계를 건너뜁니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-557">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="d63a3-558">**WebSockets 전송이 유일하게 활성화된 전송인 경우에만 지원됩니다.**</span><span class="sxs-lookup"><span data-stu-id="d63a3-558">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="d63a3-559">Azure SignalR 서비스를 사용 하는 경우이 설정을 사용 하도록 설정할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-559">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="d63a3-560">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="d63a3-560">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="d63a3-561">비어 있음</span><span class="sxs-lookup"><span data-stu-id="d63a3-561">Empty</span></span> | <span data-ttu-id="d63a3-562">모든 HTTP 요청과 함께 보낼 추가 HTTP 헤더의 맵입니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-562">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="d63a3-563">.NET 클라이언트에서 이 옵션들은 `WithUrl`에 제공되는 옵션 대리자를 이용해서 수정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-563">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="d63a3-564">JavaScript 클라이언트에서 이 옵션들은 `withUrl`에 제공되는 JavaScript 개체를 통해서 제공될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-564">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="d63a3-565">Java 클라이언트에서 이러한 옵션은에서 반환 되는 `HttpHubConnectionBuilder`에 대 한 메서드로 구성할 수 있습니다 `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="d63a3-565">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="d63a3-566">추가 자료</span><span class="sxs-lookup"><span data-stu-id="d63a3-566">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="< aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="d63a3-567">JSON/MessagePack serialization 옵션</span><span class="sxs-lookup"><span data-stu-id="d63a3-567">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="d63a3-568">ASP.NET Core SignalR는 메시지 인코딩에 [JSON](https://www.json.org/) 및 [MessagePack](https://msgpack.org/index.html)의 두 가지 프로토콜을 지원 합니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-568">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="d63a3-569">각 프로토콜에는 serialization 구성 옵션이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-569">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="d63a3-570">JSON serialization을 사용 하 여 서버에 구성할 수 있습니다 합니다 [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) 후 추가할 수 있는 확장 메서드 [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) 에서 프로그램 `Startup.ConfigureServices` 메서드.</span><span class="sxs-lookup"><span data-stu-id="d63a3-570">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="d63a3-571">`AddJsonProtocol` 메서드는 `options` 개체를 전달받는 대리자를 받습니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-571">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="d63a3-572">이 개체의 [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) 속성은 JSON.NET의 `JsonSerializerSettings` 개체로, 인수 및 반환 값의 직렬화를 구성하는 데 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-572">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="d63a3-573">자세한 내용은 [JSON.NET 설명서](https://www.newtonsoft.com/json/help/html/Introduction.htm)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="d63a3-573">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="d63a3-574">예를 들어 기본 "camelCase" 이름 대신 "대/소문자 구분" 속성 이름을 사용 하도록 serializer를 구성 하려면 `Startup.ConfigureServices`에서 다음 코드를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-574">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="d63a3-575">.NET 클라이언트에서는 [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)에 동일한 `AddJsonProtocol` 확장 메서드가 존재합니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-575">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="d63a3-576">이 확장 메서드를 확인하려면 `Microsoft.Extensions.DependencyInjection` 네임스페이스를 임포트해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-576">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

```csharp
// At the top of the file:
using Microsoft.Extensions.DependencyInjection;
 
// When constructing your connection:
var connection = new HubConnectionBuilder()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    })
    .Build();
```

> [!NOTE]
> <span data-ttu-id="d63a3-577">현재 JavaScript 클라이언트에서는 JSON 직렬화를 구성할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-577">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="d63a3-578">MessagePack serialization 옵션</span><span class="sxs-lookup"><span data-stu-id="d63a3-578">MessagePack serialization options</span></span>

<span data-ttu-id="d63a3-579">MessagePack 직렬화는 [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) 호출에 대리자를 제공하여 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-579">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="d63a3-580">자세한 내용은 [SignalR의 MessagePack를](xref:signalr/messagepackhubprotocol) 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="d63a3-580">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="d63a3-581">현재 JavaScript 클라이언트에서는 MessagePack 직렬화를 구성할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-581">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="d63a3-582">서버 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="d63a3-582">Configure server options</span></span>

<span data-ttu-id="d63a3-583">다음 표에서 SignalR 허브를 구성 하는 옵션을 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-583">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="d63a3-584">옵션</span><span class="sxs-lookup"><span data-stu-id="d63a3-584">Option</span></span> | <span data-ttu-id="d63a3-585">기본값</span><span class="sxs-lookup"><span data-stu-id="d63a3-585">Default Value</span></span> | <span data-ttu-id="d63a3-586">설명</span><span class="sxs-lookup"><span data-stu-id="d63a3-586">Description</span></span> |
| ------ | ------------- | ----------- |
| `HandshakeTimeout` | <span data-ttu-id="d63a3-587">15초</span><span class="sxs-lookup"><span data-stu-id="d63a3-587">15 seconds</span></span> | <span data-ttu-id="d63a3-588">클라이언트가 이 시간 제한 내에 초기 핸드셰이크 메시지를 전송하지 않으면 연결이 닫힙니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-588">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="d63a3-589">이 설정은 심각한 네트워크 지연으로 인해 핸드셰이크 시간 제한 오류가 발생하는 경우에만 수정해야 하는 고급 설정입니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-589">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="d63a3-590">핸드셰이크 프로세스에 대 한 자세한 내용은 [SignalR 허브 프로토콜 사양](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="d63a3-590">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="d63a3-591">15초</span><span class="sxs-lookup"><span data-stu-id="d63a3-591">15 seconds</span></span> | <span data-ttu-id="d63a3-592">서버가 이 간격 내에 메시지를 전송하지 않으면 자동으로 ping 메시지가 전송되어 연결이 열린 상태로 유지됩니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-592">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="d63a3-593">`KeepAliveInterval`을 변경할 경우 클라이언트의 `ServerTimeout`/`serverTimeoutInMilliseconds` 설정도 변경하십시오.</span><span class="sxs-lookup"><span data-stu-id="d63a3-593">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="d63a3-594">권장되는 `ServerTimeout`/`serverTimeoutInMilliseconds` 값은 `KeepAliveInterval` 값의 두 배입니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-594">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="d63a3-595">설치된 모든 프로토콜</span><span class="sxs-lookup"><span data-stu-id="d63a3-595">All installed protocols</span></span> | <span data-ttu-id="d63a3-596">허브가 지원하는 프로토콜입니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-596">Protocols supported by this hub.</span></span> <span data-ttu-id="d63a3-597">기본적으로 서버에 등록된 모든 프로토콜이 허용되지만 이 목록에서 프로토콜을 제거하여 개별 허브에 대해 특정 프로토콜을 비활성화시킬 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-597">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="d63a3-598">이 값이 `true`면 Hub 메서드에서 예외가 발생할 경우 자세한 예외 메시지가 클라이언트로 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-598">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="d63a3-599">예외 메시지에는 민감한 정보가 포함되어 있을 수 있으므로 기본값은 `false`입니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-599">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="d63a3-600">`Startup.ConfigureServices`에서 `AddSignalR` 호출에 옵션 대리자를 제공하여 모든 허브에 대한 옵션을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-600">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSignalR(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

<span data-ttu-id="d63a3-601">단일 허브에 대 한 옵션은 `AddSignalR`에서 제공 하는 전역 옵션을 재정의 하며 <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>를 사용 하 여 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-601">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="d63a3-602">고급 HTTP 구성 옵션</span><span class="sxs-lookup"><span data-stu-id="d63a3-602">Advanced HTTP configuration options</span></span>

<span data-ttu-id="d63a3-603">전송 및 메모리 버퍼 관리와 관련된 고급 설정을 구성하려면 `HttpConnectionDispatcherOptions`를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-603">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="d63a3-604">이러한 옵션은 `Startup.Configure`에서 [\<t >의 Maphub](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) 에 대리자를 전달 하 여 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-604">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseSignalR((configure) =>
    {
        var desiredTransports =
            HttpTransportType.WebSockets |
            HttpTransportType.LongPolling;

        configure.MapHub<MyHub>("/myhub", (options) =>
        {
            options.Transports = desiredTransports;
        });
    });
}
```

<span data-ttu-id="d63a3-605">다음 표에서는 ASP.NET Core SignalR의 고급 HTTP 옵션을 구성 하는 옵션을 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-605">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="d63a3-606">옵션</span><span class="sxs-lookup"><span data-stu-id="d63a3-606">Option</span></span> | <span data-ttu-id="d63a3-607">기본값</span><span class="sxs-lookup"><span data-stu-id="d63a3-607">Default Value</span></span> | <span data-ttu-id="d63a3-608">설명</span><span class="sxs-lookup"><span data-stu-id="d63a3-608">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="d63a3-609">32KB</span><span class="sxs-lookup"><span data-stu-id="d63a3-609">32 KB</span></span> | <span data-ttu-id="d63a3-610">클라이언트로부터 수신하는 서버 버퍼의 최대 바이트 수입니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-610">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="d63a3-611">이 값을 늘리면 서버가 더 큰 메시지를 수신할 수 있지만 메모리 소비에 부정적인 영향을 줄 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-611">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="d63a3-612">허브 클래스에 적용된 `Authorize` 특성에서 자동으로 수집된 데이터입니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-612">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="d63a3-613">클라이언트가 허브에 연결할 권한이 있는지 확인하는 데 사용되는 [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) 개체의 목록입니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-613">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="d63a3-614">32KB</span><span class="sxs-lookup"><span data-stu-id="d63a3-614">32 KB</span></span> | <span data-ttu-id="d63a3-615">앱에서 전송하는 서버 버퍼의 최대 바이트 수입니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-615">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="d63a3-616">이 값을 늘리면 서버가 더 큰 메시지를 전송할 수 있지만 메모리 소비에 부정적인 영향을 줄 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-616">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="d63a3-617">모든 전송을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-617">All Transports are enabled.</span></span> | <span data-ttu-id="d63a3-618">클라이언트에서 연결 하는 데 사용할 수 있는 전송을 제한할 수 있는 `HttpTransportType` 값의 비트 플래그 열거형입니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-618">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="d63a3-619">다음을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d63a3-619">See below.</span></span> | <span data-ttu-id="d63a3-620">롱 폴링 전송과 관련된 추가 옵션입니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-620">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="d63a3-621">다음을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d63a3-621">See below.</span></span> | <span data-ttu-id="d63a3-622">WebSockets 전송과 관련된 추가 옵션입니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-622">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="d63a3-623">롱 폴링 전송에는 `LongPolling` 속성을 이용해서 구성할 수 있는 추가적인 옵션이 존재합니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-623">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="d63a3-624">옵션</span><span class="sxs-lookup"><span data-stu-id="d63a3-624">Option</span></span> | <span data-ttu-id="d63a3-625">기본값</span><span class="sxs-lookup"><span data-stu-id="d63a3-625">Default Value</span></span> | <span data-ttu-id="d63a3-626">설명</span><span class="sxs-lookup"><span data-stu-id="d63a3-626">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="d63a3-627">90초</span><span class="sxs-lookup"><span data-stu-id="d63a3-627">90 seconds</span></span> | <span data-ttu-id="d63a3-628">단일 폴링 요청을 종료하기 전에 서버가 메시지를 클라이언트에 전송할 때까지 대기하는 최대 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-628">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="d63a3-629">이 값을 줄이면 클라이언트는 더 자주 새 폴링 요청을 발행합니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-629">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="d63a3-630">WebSocket 전송에는 `WebSockets` 속성을 이용해서 구성할 수 있는 추가적인 옵션이 존재합니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-630">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="d63a3-631">옵션</span><span class="sxs-lookup"><span data-stu-id="d63a3-631">Option</span></span> | <span data-ttu-id="d63a3-632">기본값</span><span class="sxs-lookup"><span data-stu-id="d63a3-632">Default Value</span></span> | <span data-ttu-id="d63a3-633">설명</span><span class="sxs-lookup"><span data-stu-id="d63a3-633">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="d63a3-634">5초</span><span class="sxs-lookup"><span data-stu-id="d63a3-634">5 seconds</span></span> | <span data-ttu-id="d63a3-635">서버가 닫힌 후 클라이언트가 이 시간 제한 내에 닫기에 실패하면 연결이 종료됩니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-635">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="d63a3-636">사용자 지정 값으로 `Sec-WebSocket-Protocol` 헤더를 설정하기 위해 사용할 수 있는 대리자입니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-636">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="d63a3-637">이 대리자는 클라이언트에서 요청한 값을 입력으로 받아서 원하는 값을 반환해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-637">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="d63a3-638">클라이언트 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="d63a3-638">Configure client options</span></span>

<span data-ttu-id="d63a3-639">클라이언트 옵션은 `HubConnectionBuilder` 형식 (.NET 및 JavaScript 클라이언트에서 사용 가능)에서 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-639">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="d63a3-640">Java 클라이언트 에서도 사용할 수 있지만 `HttpHubConnectionBuilder` 하위 클래스는 작성기 구성 옵션과 `HubConnection` 자체를 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-640">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="d63a3-641">로깅 구성</span><span class="sxs-lookup"><span data-stu-id="d63a3-641">Configure logging</span></span>

<span data-ttu-id="d63a3-642">.NET 클라이언트에서 로깅은 `ConfigureLogging` 메서드를 이용해서 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-642">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="d63a3-643">로깅 공급자 및 필터는 서버와 동일한 방법으로 등록할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-643">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="d63a3-644">자세한 내용은 [ASP.NET Core 로깅](xref:fundamentals/logging/index) 설명서를 참고하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-644">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="d63a3-645">로깅 공급자를 등록하는 데 필요한 패키지를 설치해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-645">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="d63a3-646">전체 목록은 로깅 문서의 [기본 제공 로깅 공급자](xref:fundamentals/logging/index#built-in-logging-providers) 섹션을 참고하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-646">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="d63a3-647">예를 들어 콘솔 로깅을 사용하려면 `Microsoft.Extensions.Logging.Console` NuGet 패키지를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-647">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="d63a3-648">`AddConsole` 확장 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-648">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="d63a3-649">JavaScript 클라이언트에는 유사한 `configureLogging` 메서드가 존재합니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-649">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="d63a3-650">기록할 로그 메시지의 최소 수준을 나타내는 `LogLevel` 값을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-650">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="d63a3-651">로그는 브라우저 콘솔 창에 기록됩니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-651">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="d63a3-652">로깅을 완전히 비활성화시키려면 `configureLogging` 메서드에서 `signalR.LogLevel.None`을 지정하십시오.</span><span class="sxs-lookup"><span data-stu-id="d63a3-652">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="d63a3-653">로깅에 대 한 자세한 내용은 [SignalR 진단 설명서](xref:signalr/diagnostics)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="d63a3-653">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="d63a3-654">SignalR Java 클라이언트는 [SLF4J](https://www.slf4j.org/) 라이브러리를 사용 하 여 로깅을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-654">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="d63a3-655">이는 라이브러리의 사용자가 특정 로깅 종속성을 가져와 자신의 특정 로깅 구현을 선택할 수 있게 해 주는 상위 수준 로깅 API입니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-655">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="d63a3-656">다음 코드 조각에서는 SignalR Java 클라이언트와 함께 `java.util.logging`를 사용 하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-656">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="d63a3-657">종속성에서 로깅을 구성 하지 않으면 SLF4J는 다음과 같은 경고 메시지와 함께 기본 작업 없음로 거를 로드 합니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-657">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="d63a3-658">이는 무시 해도 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-658">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="d63a3-659">허용되는 전송 구성</span><span class="sxs-lookup"><span data-stu-id="d63a3-659">Configure allowed transports</span></span>

<span data-ttu-id="d63a3-660">SignalR에서 사용 하는 전송은 `WithUrl` 호출 (JavaScript의`withUrl`)에서 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-660">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="d63a3-661">`HttpTransportType` 값들의 비트 OR을 이용해서 클라이언트가 지정한 전송만 사용하도록 제한할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-661">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="d63a3-662">기본적으로 모든 전송은 활성화되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-662">All transports are enabled by default.</span></span>

<span data-ttu-id="d63a3-663">예를 들어 다음 코드는 서버-전송 이벤트 전송을 비활성화시키고 WebSocket 및 롱 폴링 연결만 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-663">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="d63a3-664">JavaScript 클라이언트에서 전송은 `withUrl`에 제공되는 옵션 개체의 `transport` 필드를 설정하여 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-664">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

### <a name="configure-bearer-authentication"></a><span data-ttu-id="d63a3-665">전달자 인증 구성</span><span class="sxs-lookup"><span data-stu-id="d63a3-665">Configure bearer authentication</span></span>

<span data-ttu-id="d63a3-666">SignalR 요청과 함께 인증 데이터를 제공 하려면 JavaScript에서`accessTokenFactory` `AccessTokenProvider` 옵션을 사용 하 여 원하는 액세스 토큰을 반환 하는 함수를 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-666">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="d63a3-667">.NET 클라이언트에서는 액세스 토큰이 HTTP "Bearer Authentication" 토큰으로 전달됩니다(`Bearer` 형식의 `Authorization` 헤더를 통해).</span><span class="sxs-lookup"><span data-stu-id="d63a3-667">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="d63a3-668">JavaScript 클라이언트에서는 브라우저의 API가 헤더 적용 기능을 제한하는 몇 가지 경우를 **제외**하면 (특히 서버-전송 이벤트 및 WebSockets 요청) 액세스 토큰이 전달자 토큰으로 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-668">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="d63a3-669">이런 경우 액세스 토큰은 `access_token`이라는 쿼리 문자열 값으로 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-669">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="d63a3-670">.NET 클라이언트에서 `AccessTokenProvider` 옵션은 `WithUrl`에서 옵션 대리자를 이용해서 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-670">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="d63a3-671">JavaScript 클라이언트에서는 `withUrl`에서 옵션 개체의 `accessTokenFactory` 필드를 설정하여 액세스 토큰을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-671">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

<span data-ttu-id="d63a3-672">SignalR Java 클라이언트에서 [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)에 액세스 토큰 팩터리를 제공 하 여 인증에 사용할 전달자 토큰을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-672">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="d63a3-673">[WithAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) 를 사용 하 여 [rxjava](https://github.com/ReactiveX/RxJava) [Single\<문자열 >](https://reactivex.io/documentation/single.html)를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-673">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="d63a3-674">[단일. 지연](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)에 대 한 호출을 사용 하 여 클라이언트에 대 한 액세스 토큰을 생성 하는 논리를 작성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-674">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="d63a3-675">시간 제한 및 연결 유지 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="d63a3-675">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="d63a3-676">시간 제한 및 연결 유지 동작을 구성하기 위한 추가 옵션은 `HubConnection` 개체 자체에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-676">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="nettabdotnet"></a>[<span data-ttu-id="d63a3-677">.NET</span><span class="sxs-lookup"><span data-stu-id="d63a3-677">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="d63a3-678">옵션</span><span class="sxs-lookup"><span data-stu-id="d63a3-678">Option</span></span> | <span data-ttu-id="d63a3-679">기본값</span><span class="sxs-lookup"><span data-stu-id="d63a3-679">Default value</span></span> | <span data-ttu-id="d63a3-680">설명</span><span class="sxs-lookup"><span data-stu-id="d63a3-680">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="d63a3-681">30초(30000밀리초)</span><span class="sxs-lookup"><span data-stu-id="d63a3-681">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="d63a3-682">서버 활동 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-682">Timeout for server activity.</span></span> <span data-ttu-id="d63a3-683">서버가 이 시간 제한 내에 메시지를 전송하지 않으면 클라이언트는 서버 연결이 끊어졌다고 간주하고 `Closed` 이벤트(JavaScript에서는 `onclose` 이벤트)를  트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-683">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="d63a3-684">이 값이 충분히 커야만 ping 메시지가 서버에서 전송**되고** 시간 제한 내에 클라이언트에 의해 수신될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-684">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="d63a3-685">권장 값은 ping이 도착할 때까지 시간을 허용 하기 위해 서버의 `KeepAliveInterval` 값을 두 번 이상 사용 하는 숫자입니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-685">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="d63a3-686">15초</span><span class="sxs-lookup"><span data-stu-id="d63a3-686">15 seconds</span></span> | <span data-ttu-id="d63a3-687">초기 서버 핸드셰이크에 대한 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-687">Timeout for initial server handshake.</span></span> <span data-ttu-id="d63a3-688">서버가 이 시간 제한 내에 핸드셰이크 응답을 전송하지 않으면 클라이언트는 핸드셰이크를 취소하고 `Closed` 이벤트(JavaScript에서는 `onclose` 이벤트)를 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-688">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="d63a3-689">이 설정은 심각한 네트워크 지연으로 인해 핸드셰이크 시간 제한 오류가 발생하는 경우에만 수정해야 하는 고급 설정입니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-689">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="d63a3-690">핸드셰이크 프로세스에 대 한 자세한 내용은 [SignalR 허브 프로토콜 사양](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="d63a3-690">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

<span data-ttu-id="d63a3-691">.NET 클라이언트에서 시간 제한 값은 `TimeSpan` 값으로 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-691">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascripttabjavascript"></a>[<span data-ttu-id="d63a3-692">JavaScript</span><span class="sxs-lookup"><span data-stu-id="d63a3-692">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="d63a3-693">옵션</span><span class="sxs-lookup"><span data-stu-id="d63a3-693">Option</span></span> | <span data-ttu-id="d63a3-694">기본값</span><span class="sxs-lookup"><span data-stu-id="d63a3-694">Default value</span></span> | <span data-ttu-id="d63a3-695">설명</span><span class="sxs-lookup"><span data-stu-id="d63a3-695">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="d63a3-696">30초(30000밀리초)</span><span class="sxs-lookup"><span data-stu-id="d63a3-696">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="d63a3-697">서버 활동 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-697">Timeout for server activity.</span></span> <span data-ttu-id="d63a3-698">서버가이 간격 내에 메시지를 보내지 않은 경우 클라이언트는 서버 연결을 끊고 `onclose` 이벤트를 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-698">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="d63a3-699">이 값이 충분히 커야만 ping 메시지가 서버에서 전송**되고** 시간 제한 내에 클라이언트에 의해 수신될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-699">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="d63a3-700">권장 값은 ping이 도착할 때까지 시간을 허용 하기 위해 서버의 `KeepAliveInterval` 값을 두 번 이상 사용 하는 숫자입니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-700">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |

# <a name="javatabjava"></a>[<span data-ttu-id="d63a3-701">Java</span><span class="sxs-lookup"><span data-stu-id="d63a3-701">Java</span></span>](#tab/java)

| <span data-ttu-id="d63a3-702">옵션</span><span class="sxs-lookup"><span data-stu-id="d63a3-702">Option</span></span> | <span data-ttu-id="d63a3-703">기본값</span><span class="sxs-lookup"><span data-stu-id="d63a3-703">Default value</span></span> | <span data-ttu-id="d63a3-704">설명</span><span class="sxs-lookup"><span data-stu-id="d63a3-704">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="d63a3-705">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="d63a3-705">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="d63a3-706">30초(30000밀리초)</span><span class="sxs-lookup"><span data-stu-id="d63a3-706">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="d63a3-707">서버 활동 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-707">Timeout for server activity.</span></span> <span data-ttu-id="d63a3-708">서버가이 간격 내에 메시지를 보내지 않은 경우 클라이언트는 서버 연결을 끊고 `onClose` 이벤트를 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-708">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="d63a3-709">이 값이 충분히 커야만 ping 메시지가 서버에서 전송**되고** 시간 제한 내에 클라이언트에 의해 수신될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-709">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="d63a3-710">권장되는 값은 ping이 도착할 시간을 확보하기 위해 최소한 서버의 `KeepAliveInterval` 값의 두 배가 되는 숫자여야 합니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-710">The recommended value is a number at least double the server's `KeepAliveInterval` value, to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="d63a3-711">15초</span><span class="sxs-lookup"><span data-stu-id="d63a3-711">15 seconds</span></span> | <span data-ttu-id="d63a3-712">초기 서버 핸드셰이크에 대한 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-712">Timeout for initial server handshake.</span></span> <span data-ttu-id="d63a3-713">서버가이 간격 내에 핸드셰이크 응답을 보내지 않는 경우 클라이언트는 핸드셰이크를 취소 하 고 `onClose` 이벤트를 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-713">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="d63a3-714">이 설정은 심각한 네트워크 지연으로 인해 핸드셰이크 시간 제한 오류가 발생하는 경우에만 수정해야 하는 고급 설정입니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-714">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="d63a3-715">핸드셰이크 프로세스에 대 한 자세한 내용은 [SignalR 허브 프로토콜 사양](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="d63a3-715">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="d63a3-716">추가 구성 옵션</span><span class="sxs-lookup"><span data-stu-id="d63a3-716">Configure additional options</span></span>

<span data-ttu-id="d63a3-717">추가 옵션은 Java 클라이언트의 `HttpHubConnectionBuilder`에서 `HubConnectionBuilder` 또는 다양 한 구성 Api에 대 한 `WithUrl` (JavaScript의`withUrl`) 방법으로 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-717">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="nettabdotnet"></a>[<span data-ttu-id="d63a3-718">.NET</span><span class="sxs-lookup"><span data-stu-id="d63a3-718">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="d63a3-719">.NET 옵션</span><span class="sxs-lookup"><span data-stu-id="d63a3-719">.NET Option</span></span> |  <span data-ttu-id="d63a3-720">기본값</span><span class="sxs-lookup"><span data-stu-id="d63a3-720">Default value</span></span> | <span data-ttu-id="d63a3-721">설명</span><span class="sxs-lookup"><span data-stu-id="d63a3-721">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="d63a3-722">HTTP 요청에서 전달자 인증 토큰으로 제공된 문자열을 반환하는 함수입니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-722">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="d63a3-723">이 값을 `true`로 설정하면 협상 단계를 건너뜁니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-723">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="d63a3-724">**WebSockets 전송이 유일하게 활성화된 전송인 경우에만 지원됩니다.**</span><span class="sxs-lookup"><span data-stu-id="d63a3-724">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="d63a3-725">Azure SignalR 서비스를 사용 하는 경우이 설정을 사용 하도록 설정할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-725">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="d63a3-726">비어 있음</span><span class="sxs-lookup"><span data-stu-id="d63a3-726">Empty</span></span> | <span data-ttu-id="d63a3-727">요청을 인증하기 위해 전송할 TLS 인증서의 컬렉션입니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-727">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="d63a3-728">비어 있음</span><span class="sxs-lookup"><span data-stu-id="d63a3-728">Empty</span></span> | <span data-ttu-id="d63a3-729">모든 HTTP 요청과 함께 전송할 HTTP 쿠키의 컬렉션입니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-729">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="d63a3-730">비어 있음</span><span class="sxs-lookup"><span data-stu-id="d63a3-730">Empty</span></span> | <span data-ttu-id="d63a3-731">모든 HTTP 요청과 함께 전송할 자격 증명입니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-731">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="d63a3-732">5초</span><span class="sxs-lookup"><span data-stu-id="d63a3-732">5 seconds</span></span> | <span data-ttu-id="d63a3-733">Websocket에만 해당됩니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-733">WebSockets only.</span></span> <span data-ttu-id="d63a3-734">클라이언트가 서버를 닫은 후 닫기 요청을 확인하기 위해 대기하는 최대 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-734">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="d63a3-735">서버가 이 시간 내에 종료를 승인하지 않으면 클라이언트가 연결을 끊습니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-735">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="d63a3-736">비어 있음</span><span class="sxs-lookup"><span data-stu-id="d63a3-736">Empty</span></span> | <span data-ttu-id="d63a3-737">모든 HTTP 요청과 함께 보낼 추가 HTTP 헤더의 맵입니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-737">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="d63a3-738">HTTP 요청을 전송하기 위해 사용되는 `HttpMessageHandler`를 구성하거나 대체하는 데 사용할 수 있는 대리자입니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-738">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="d63a3-739">WebSocket 연결에는 사용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-739">Not used for WebSocket connections.</span></span> <span data-ttu-id="d63a3-740">이 대리자는 null이 아닌 값을 반환해야 하며 매개 변수로 기본값을 받습니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-740">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="d63a3-741">이 기본값의 설정을 수정하여 반환하거나 새로운 `HttpMessageHandler` 인스턴스를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-741">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="d63a3-742">**처리기를 대체할 때 제공 된 처리기에서 유지할 설정을 복사 해야 합니다. 그렇지 않으면 구성 된 옵션 (예: 쿠키 및 헤더)이 새 처리기에 적용 되지 않습니다.**</span><span class="sxs-lookup"><span data-stu-id="d63a3-742">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="d63a3-743">HTTP 요청을 전송할 때 사용할 HTTP 프록시입니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-743">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="d63a3-744">HTTP 및 WebSockets 요청에 대한 기본 자격 증명을 전송하려면 이 부울 값을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-744">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="d63a3-745">그러면 Windows 인증을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-745">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="d63a3-746">추가적인 WebSocket 옵션을 구성할 수 있는 대리자입니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-746">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="d63a3-747">옵션을 구성에 사용할 수 있는 [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions)의 인스턴스를 전달받습니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-747">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascripttabjavascript"></a>[<span data-ttu-id="d63a3-748">JavaScript</span><span class="sxs-lookup"><span data-stu-id="d63a3-748">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="d63a3-749">JavaScript 옵션</span><span class="sxs-lookup"><span data-stu-id="d63a3-749">JavaScript Option</span></span> | <span data-ttu-id="d63a3-750">기본값</span><span class="sxs-lookup"><span data-stu-id="d63a3-750">Default Value</span></span> | <span data-ttu-id="d63a3-751">설명</span><span class="sxs-lookup"><span data-stu-id="d63a3-751">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="d63a3-752">HTTP 요청에서 전달자 인증 토큰으로 제공된 문자열을 반환하는 함수입니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-752">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="d63a3-753">이 값을 `true`로 설정하면 협상 단계를 건너뜁니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-753">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="d63a3-754">**WebSockets 전송이 유일하게 활성화된 전송인 경우에만 지원됩니다.**</span><span class="sxs-lookup"><span data-stu-id="d63a3-754">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="d63a3-755">Azure SignalR 서비스를 사용 하는 경우이 설정을 사용 하도록 설정할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-755">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="javatabjava"></a>[<span data-ttu-id="d63a3-756">Java</span><span class="sxs-lookup"><span data-stu-id="d63a3-756">Java</span></span>](#tab/java)

| <span data-ttu-id="d63a3-757">Java 옵션</span><span class="sxs-lookup"><span data-stu-id="d63a3-757">Java Option</span></span> | <span data-ttu-id="d63a3-758">기본값</span><span class="sxs-lookup"><span data-stu-id="d63a3-758">Default Value</span></span> | <span data-ttu-id="d63a3-759">설명</span><span class="sxs-lookup"><span data-stu-id="d63a3-759">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="d63a3-760">HTTP 요청에서 전달자 인증 토큰으로 제공된 문자열을 반환하는 함수입니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-760">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="d63a3-761">이 값을 `true`로 설정하면 협상 단계를 건너뜁니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-761">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="d63a3-762">**WebSockets 전송이 유일하게 활성화된 전송인 경우에만 지원됩니다.**</span><span class="sxs-lookup"><span data-stu-id="d63a3-762">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="d63a3-763">Azure SignalR 서비스를 사용 하는 경우이 설정을 사용 하도록 설정할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-763">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="d63a3-764">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="d63a3-764">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="d63a3-765">비어 있음</span><span class="sxs-lookup"><span data-stu-id="d63a3-765">Empty</span></span> | <span data-ttu-id="d63a3-766">모든 HTTP 요청과 함께 보낼 추가 HTTP 헤더의 맵입니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-766">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="d63a3-767">.NET 클라이언트에서 이 옵션들은 `WithUrl`에 제공되는 옵션 대리자를 이용해서 수정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-767">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="d63a3-768">JavaScript 클라이언트에서 이 옵션들은 `withUrl`에 제공되는 JavaScript 개체를 통해서 제공될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d63a3-768">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="d63a3-769">Java 클라이언트에서 이러한 옵션은에서 반환 되는 `HttpHubConnectionBuilder`에 대 한 메서드로 구성할 수 있습니다 `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="d63a3-769">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="d63a3-770">추가 자료</span><span class="sxs-lookup"><span data-stu-id="d63a3-770">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end