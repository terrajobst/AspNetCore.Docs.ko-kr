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
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78651687"
---
# <a name="aspnet-core-signalr-configuration"></a><span data-ttu-id="7bddb-103">ASP.NET Core SignalR 구성</span><span class="sxs-lookup"><span data-stu-id="7bddb-103">ASP.NET Core SignalR configuration</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="7bddb-104">JSON/MessagePack serialization 옵션</span><span class="sxs-lookup"><span data-stu-id="7bddb-104">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="7bddb-105">ASP.NET Core SignalR는 메시지 인코딩에 사용할 두 가지 프로토콜인 [JSON](https://www.json.org/) 및 [MessagePack](https://msgpack.org/index.html)를 지원 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-105">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="7bddb-106">각 프로토콜에는 serialization 구성 옵션이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-106">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="7bddb-107">JSON serialization은 [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) 확장 메서드를 사용 하 여 서버에서 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-107">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="7bddb-108">`AddJsonProtocol`는 `Startup.ConfigureServices`[AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) 뒤에 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-108">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="7bddb-109">`AddJsonProtocol` 메서드는 `options` 개체를 받는 대리자를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-109">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="7bddb-110">해당 개체의 [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) 속성은 인수 및 반환 값의 serialization을 구성 하는 데 사용할 수 있는 `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> 개체입니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-110">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="7bddb-111">자세한 내용은 [system.object 설명서](/dotnet/api/system.text.json)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="7bddb-111">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="7bddb-112">예를 들어 기본 "camelCase" 이름 대신 속성 이름의 대/소문자를 변경 하지 않도록 serializer를 구성 하려면 `Startup.ConfigureServices`에서 다음 코드를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-112">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="7bddb-113">.NET 클라이언트에서는 [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)에 동일한 `AddJsonProtocol` 확장 메서드가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-113">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="7bddb-114">확장 메서드를 확인 하려면 `Microsoft.Extensions.DependencyInjection` 네임 스페이스를 가져와야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-114">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="7bddb-115">현재 JavaScript 클라이언트에서는 JSON 직렬화를 구성할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-115">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="7bddb-116">Newtonsoft.json로 전환 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-116">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="7bddb-117">`System.Text.Json`에서 지원 되지 않는 `Newtonsoft.Json` 기능이 필요한 경우 [newtonsoft.json으로 전환](xref:migration/22-to-30#switch-to-newtonsoftjson)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="7bddb-117">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="7bddb-118">MessagePack serialization 옵션</span><span class="sxs-lookup"><span data-stu-id="7bddb-118">MessagePack serialization options</span></span>

<span data-ttu-id="7bddb-119">MessagePack serialization은 [Addmessagepackprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) 호출에 대리자를 제공 하 여 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-119">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="7bddb-120">자세한 내용은 [MessagePack In SignalR](xref:signalr/messagepackhubprotocol) 을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="7bddb-120">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="7bddb-121">현재 JavaScript 클라이언트에서는 MessagePack 직렬화를 구성할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-121">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="7bddb-122">서버 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="7bddb-122">Configure server options</span></span>

<span data-ttu-id="7bddb-123">다음 표는 SignalR 허브를 구성하기 위한 옵션을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-123">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="7bddb-124">옵션</span><span class="sxs-lookup"><span data-stu-id="7bddb-124">Option</span></span> | <span data-ttu-id="7bddb-125">기본값</span><span class="sxs-lookup"><span data-stu-id="7bddb-125">Default Value</span></span> | <span data-ttu-id="7bddb-126">Description</span><span class="sxs-lookup"><span data-stu-id="7bddb-126">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="7bddb-127">30초</span><span class="sxs-lookup"><span data-stu-id="7bddb-127">30 seconds</span></span> | <span data-ttu-id="7bddb-128">서버는이 간격 내에 메시지를 받지 못한 경우 (keep-alive 포함) 클라이언트의 연결을 해제 하는 것으로 간주 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-128">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="7bddb-129">이를 구현 하는 방법으로 인해 클라이언트에서 실제로 연결이 끊어진 것으로 표시 되는 시간 제한 간격 보다 오래 걸릴 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-129">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="7bddb-130">권장 값은 두 개의 `KeepAliveInterval` 값입니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-130">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="7bddb-131">15초</span><span class="sxs-lookup"><span data-stu-id="7bddb-131">15 seconds</span></span> | <span data-ttu-id="7bddb-132">클라이언트가 이 시간 제한 내에 초기 핸드셰이크 메시지를 전송하지 않으면 연결이 닫힙니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-132">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="7bddb-133">이 설정은 심각한 네트워크 지연으로 인해 핸드셰이크 시간 제한 오류가 발생하는 경우에만 수정해야 하는 고급 설정입니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-133">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="7bddb-134">핸드셰이크 프로세스에 대 한 자세한 내용은 [SignalR Hub 프로토콜 사양](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="7bddb-134">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="7bddb-135">15초</span><span class="sxs-lookup"><span data-stu-id="7bddb-135">15 seconds</span></span> | <span data-ttu-id="7bddb-136">서버가 이 간격 내에 메시지를 전송하지 않으면 자동으로 ping 메시지가 전송되어 연결이 열린 상태로 유지됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-136">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="7bddb-137">`KeepAliveInterval`를 변경 하는 경우 클라이언트에서 `ServerTimeout`/`serverTimeoutInMilliseconds` 설정을 변경 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-137">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="7bddb-138">권장 되는 `ServerTimeout`/`serverTimeoutInMilliseconds` 값은 `KeepAliveInterval` 값 두 배가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-138">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="7bddb-139">설치된 모든 프로토콜</span><span class="sxs-lookup"><span data-stu-id="7bddb-139">All installed protocols</span></span> | <span data-ttu-id="7bddb-140">허브가 지원하는 프로토콜입니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-140">Protocols supported by this hub.</span></span> <span data-ttu-id="7bddb-141">기본적으로 서버에 등록된 모든 프로토콜이 허용되지만 이 목록에서 프로토콜을 제거하여 개별 허브에 대해 특정 프로토콜을 비활성화시킬 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-141">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="7bddb-142">`true`경우 허브 메서드에서 예외가 throw 되 면 자세한 예외 메시지가 클라이언트에 반환 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-142">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="7bddb-143">이러한 예외 메시지에는 중요 한 정보가 포함 될 수 있으므로 기본값은 `false`입니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-143">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="7bddb-144">클라이언트 업로드 스트림에 대해 버퍼링 할 수 있는 최대 항목 수입니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-144">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="7bddb-145">이 제한에 도달 하면 서버에서 스트림 항목을 처리할 때까지 호출 처리가 차단 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-145">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="7bddb-146">32KB</span><span class="sxs-lookup"><span data-stu-id="7bddb-146">32 KB</span></span> | <span data-ttu-id="7bddb-147">들어오는 단일 허브 메시지의 최대 크기입니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-147">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="7bddb-148">`Startup.ConfigureServices`에서 `AddSignalR` 호출에 옵션 대리자를 제공 하 여 모든 허브에 대 한 옵션을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-148">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="7bddb-149">단일 허브에 대 한 옵션은 `AddSignalR`에서 제공 하는 전역 옵션을 재정의 하며 <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>를 사용 하 여 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-149">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="7bddb-150">고급 HTTP 구성 옵션</span><span class="sxs-lookup"><span data-stu-id="7bddb-150">Advanced HTTP configuration options</span></span>

<span data-ttu-id="7bddb-151">`HttpConnectionDispatcherOptions`를 사용 하 여 전송 및 메모리 버퍼 관리와 관련 된 고급 설정을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-151">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="7bddb-152">이러한 옵션은 `Startup.Configure`에서 [\<t >의 Maphub](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) 에 대리자를 전달 하 여 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-152">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="7bddb-153">다음 표에서는 ASP.NET Core SignalR의 고급 HTTP 옵션을 구성 하는 옵션을 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-153">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="7bddb-154">옵션</span><span class="sxs-lookup"><span data-stu-id="7bddb-154">Option</span></span> | <span data-ttu-id="7bddb-155">기본값</span><span class="sxs-lookup"><span data-stu-id="7bddb-155">Default Value</span></span> | <span data-ttu-id="7bddb-156">Description</span><span class="sxs-lookup"><span data-stu-id="7bddb-156">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="7bddb-157">32KB</span><span class="sxs-lookup"><span data-stu-id="7bddb-157">32 KB</span></span> | <span data-ttu-id="7bddb-158">클라이언트에서 수신 된 백 압력을 적용 하기 전에 버퍼링 되는 최대 바이트 수입니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-158">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="7bddb-159">이 값을 늘리면 서버는 백 압력을 적용 하지 않고 더 큰 메시지를 더 빨리 수신할 수 있지만 메모리 사용을 높일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-159">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="7bddb-160">허브 클래스에 적용 된 `Authorize` 특성에서 자동으로 수집 된 데이터입니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-160">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="7bddb-161">클라이언트에 허브에 연결할 수 있는 권한이 있는지 여부를 확인 하는 데 사용 되는 [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) 개체의 목록입니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-161">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="7bddb-162">32KB</span><span class="sxs-lookup"><span data-stu-id="7bddb-162">32 KB</span></span> | <span data-ttu-id="7bddb-163">서버에서 백 압력을 관찰 하기 전에 버퍼링 하는 앱에서 보낸 최대 바이트 수입니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-163">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="7bddb-164">이 값을 늘리면 서버에서 백 압력을 대기 하지 않고 더 큰 메시지를 더 빨리 버퍼링 할 수 있지만 메모리 소비가 증가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-164">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="7bddb-165">모든 전송을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-165">All Transports are enabled.</span></span> | <span data-ttu-id="7bddb-166">클라이언트에서 연결 하는 데 사용할 수 있는 전송을 제한할 수 있는 `HttpTransportType` 값의 비트 플래그 열거형입니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-166">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="7bddb-167">아래를 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="7bddb-167">See below.</span></span> | <span data-ttu-id="7bddb-168">롱 폴링 전송과 관련된 추가 옵션입니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-168">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="7bddb-169">아래를 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="7bddb-169">See below.</span></span> | <span data-ttu-id="7bddb-170">WebSockets 전송과 관련된 추가 옵션입니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-170">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="7bddb-171">긴 폴링 전송에는 `LongPolling` 속성을 사용 하 여 구성할 수 있는 추가 옵션이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-171">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="7bddb-172">옵션</span><span class="sxs-lookup"><span data-stu-id="7bddb-172">Option</span></span> | <span data-ttu-id="7bddb-173">기본값</span><span class="sxs-lookup"><span data-stu-id="7bddb-173">Default Value</span></span> | <span data-ttu-id="7bddb-174">Description</span><span class="sxs-lookup"><span data-stu-id="7bddb-174">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="7bddb-175">90초</span><span class="sxs-lookup"><span data-stu-id="7bddb-175">90 seconds</span></span> | <span data-ttu-id="7bddb-176">단일 폴링 요청을 종료하기 전에 서버가 메시지를 클라이언트에 전송할 때까지 대기하는 최대 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-176">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="7bddb-177">이 값을 줄이면 클라이언트는 더 자주 새 폴링 요청을 발행합니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-177">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="7bddb-178">WebSocket 전송은 `WebSockets` 속성을 사용 하 여 구성할 수 있는 추가 옵션을 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-178">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="7bddb-179">옵션</span><span class="sxs-lookup"><span data-stu-id="7bddb-179">Option</span></span> | <span data-ttu-id="7bddb-180">기본값</span><span class="sxs-lookup"><span data-stu-id="7bddb-180">Default Value</span></span> | <span data-ttu-id="7bddb-181">Description</span><span class="sxs-lookup"><span data-stu-id="7bddb-181">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="7bddb-182">5초</span><span class="sxs-lookup"><span data-stu-id="7bddb-182">5 seconds</span></span> | <span data-ttu-id="7bddb-183">서버가 닫힌 후 클라이언트가 이 시간 제한 내에 닫기에 실패하면 연결이 종료됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-183">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="7bddb-184">`Sec-WebSocket-Protocol` 헤더를 사용자 지정 값으로 설정 하는 데 사용할 수 있는 대리자입니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-184">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="7bddb-185">이 대리자는 클라이언트에서 요청한 값을 입력으로 받아서 원하는 값을 반환해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-185">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="7bddb-186">클라이언트 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="7bddb-186">Configure client options</span></span>

<span data-ttu-id="7bddb-187">클라이언트 옵션은 `HubConnectionBuilder` 형식 (.NET 및 JavaScript 클라이언트에서 사용 가능)에서 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-187">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="7bddb-188">Java 클라이언트 에서도 사용할 수 있지만 `HttpHubConnectionBuilder` 하위 클래스는 작성기 구성 옵션과 `HubConnection` 자체를 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-188">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="7bddb-189">로깅 구성</span><span class="sxs-lookup"><span data-stu-id="7bddb-189">Configure logging</span></span>

<span data-ttu-id="7bddb-190">로깅은 `ConfigureLogging` 메서드를 사용 하 여 .NET 클라이언트에서 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-190">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="7bddb-191">로깅 공급자 및 필터는 서버와 동일한 방법으로 등록할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-191">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="7bddb-192">자세한 내용은 [ASP.NET Core의 로깅](xref:fundamentals/logging/index) 문서를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="7bddb-192">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="7bddb-193">로깅 공급자를 등록하는 데 필요한 패키지를 설치해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-193">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="7bddb-194">전체 목록은 문서에서 [기본 제공 로깅 공급자](xref:fundamentals/logging/index#built-in-logging-providers) 섹션을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="7bddb-194">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="7bddb-195">예를 들어 콘솔 로깅을 사용 하도록 설정 하려면 `Microsoft.Extensions.Logging.Console` NuGet 패키지를 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-195">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="7bddb-196">`AddConsole` 확장 메서드를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-196">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="7bddb-197">JavaScript 클라이언트에서 유사한 `configureLogging` 메서드가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-197">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="7bddb-198">생성할 로그 메시지의 최소 수준을 나타내는 `LogLevel` 값을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-198">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="7bddb-199">로그는 브라우저 콘솔 창에 기록됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-199">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="7bddb-200">`LogLevel` 값 대신 로그 수준 이름을 나타내는 `string` 값을 제공할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-200">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="7bddb-201">`LogLevel` 상수에 액세스할 수 없는 환경에서 SignalR 로깅을 구성할 때 유용 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-201">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="7bddb-202">다음 표에서는 사용 가능한 로그 수준을 나열 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-202">The following table lists the available log levels.</span></span> <span data-ttu-id="7bddb-203">사용자가 `configureLogging`에 제공 하는 값은 로깅할 **최소** 로그 수준을 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-203">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="7bddb-204">이 수준에서 기록 되 **는 메시지 또는 테이블의 이후 나열 된 수준**에 기록 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-204">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="7bddb-205">String</span><span class="sxs-lookup"><span data-stu-id="7bddb-205">String</span></span>                      | <span data-ttu-id="7bddb-206">LogLevel</span><span class="sxs-lookup"><span data-stu-id="7bddb-206">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="7bddb-207">`info` **또는** `information`</span><span class="sxs-lookup"><span data-stu-id="7bddb-207">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="7bddb-208">`warn` **또는** `warning`</span><span class="sxs-lookup"><span data-stu-id="7bddb-208">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="7bddb-209">로깅을 완전히 사용 하지 않도록 설정 하려면 `configureLogging` 메서드에서 `signalR.LogLevel.None`를 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-209">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="7bddb-210">로깅에 대 한 자세한 내용은 [SignalR 진단 설명서](xref:signalr/diagnostics)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="7bddb-210">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="7bddb-211">SignalR Java 클라이언트는 [SLF4J](https://www.slf4j.org/) 라이브러리를 사용 하 여 로깅을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-211">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="7bddb-212">이는 라이브러리의 사용자가 특정 로깅 종속성을 가져와 자신의 특정 로깅 구현을 선택할 수 있게 해 주는 상위 수준 로깅 API입니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-212">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="7bddb-213">다음 코드 조각에서는 SignalR Java 클라이언트와 함께 `java.util.logging`를 사용 하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-213">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="7bddb-214">종속성에서 로깅을 구성 하지 않으면 SLF4J는 다음과 같은 경고 메시지와 함께 기본 작업 없음로 거를 로드 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-214">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="7bddb-215">이는 무시 해도 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-215">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="7bddb-216">허용되는 전송 구성</span><span class="sxs-lookup"><span data-stu-id="7bddb-216">Configure allowed transports</span></span>

<span data-ttu-id="7bddb-217">SignalR에서 사용 하는 전송은 `WithUrl` 호출 (JavaScript의`withUrl`)에서 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-217">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="7bddb-218">`HttpTransportType` 값의 비트 or를 사용 하 여 지정 된 전송만 사용 하도록 클라이언트를 제한할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-218">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="7bddb-219">기본적으로 모든 전송은 활성화되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-219">All transports are enabled by default.</span></span>

<span data-ttu-id="7bddb-220">예를 들어 다음 코드는 서버-전송 이벤트 전송을 비활성화시키고 WebSocket 및 롱 폴링 연결만 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-220">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="7bddb-221">JavaScript 클라이언트에서 `withUrl`에 제공 된 options 개체의 `transport` 필드를 설정 하 여 전송이 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-221">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="7bddb-222">이 버전의 Java 클라이언트 websocket은 유일 하 게 사용할 수 있는 전송입니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-222">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="7bddb-223">Java 클라이언트에서 `HttpHubConnectionBuilder`의 `withTransport` 메서드를 사용 하 여 전송이 선택 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-223">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="7bddb-224">Java 클라이언트는 기본적으로 Websocket 전송을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-224">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="7bddb-225">SignalR Java 클라이언트는 아직 전송 대체를 지원 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-225">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="7bddb-226">전달자 인증 구성</span><span class="sxs-lookup"><span data-stu-id="7bddb-226">Configure bearer authentication</span></span>

<span data-ttu-id="7bddb-227">SignalR 요청과 함께 인증 데이터를 제공 하려면 `AccessTokenProvider` 옵션 (JavaScript에서`accessTokenFactory`)을 사용 하 여 원하는 액세스 토큰을 반환 하는 함수를 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-227">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="7bddb-228">.NET 클라이언트에서이 액세스 토큰은 HTTP "전달자 인증" 토큰으로 전달 됩니다 (`Bearer`형식으로 `Authorization` 헤더 사용).</span><span class="sxs-lookup"><span data-stu-id="7bddb-228">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="7bddb-229">JavaScript 클라이언트에서 액세스 토큰은 브라우저 Api가 헤더를 적용 하는 기능을 제한 하는 경우를 **제외** 하 고 전달자 토큰으로 사용 됩니다 (특히 서버에서 보낸 이벤트와 websocket 요청에서).</span><span class="sxs-lookup"><span data-stu-id="7bddb-229">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="7bddb-230">이러한 경우 액세스 토큰은 `access_token`쿼리 문자열 값으로 제공 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-230">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="7bddb-231">.NET 클라이언트에서 `AccessTokenProvider` 옵션은 `WithUrl`의 옵션 대리자를 사용 하 여 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-231">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="7bddb-232">JavaScript 클라이언트에서 액세스 토큰은 `withUrl`의 options 개체에 `accessTokenFactory` 필드를 설정 하 여 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-232">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="7bddb-233">SignalR Java 클라이언트에서 [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)에 액세스 토큰 팩터리를 제공 하 여 인증에 사용할 전달자 토큰을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-233">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="7bddb-234">[WithAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) 를 사용 하 여 [rxjava](https://github.com/ReactiveX/RxJava) [Single\<문자열 >](https://reactivex.io/documentation/single.html)를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-234">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="7bddb-235">[단일. 지연](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)에 대 한 호출을 사용 하 여 클라이언트에 대 한 액세스 토큰을 생성 하는 논리를 작성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-235">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="7bddb-236">시간 제한 및 연결 유지 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="7bddb-236">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="7bddb-237">제한 시간 및 연결 유지 동작을 구성 하기 위한 추가 옵션은 `HubConnection` 개체 자체에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-237">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="7bddb-238">.NET</span><span class="sxs-lookup"><span data-stu-id="7bddb-238">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="7bddb-239">옵션</span><span class="sxs-lookup"><span data-stu-id="7bddb-239">Option</span></span> | <span data-ttu-id="7bddb-240">기본값</span><span class="sxs-lookup"><span data-stu-id="7bddb-240">Default value</span></span> | <span data-ttu-id="7bddb-241">Description</span><span class="sxs-lookup"><span data-stu-id="7bddb-241">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="7bddb-242">30초(30000밀리초)</span><span class="sxs-lookup"><span data-stu-id="7bddb-242">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="7bddb-243">서버 활동 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-243">Timeout for server activity.</span></span> <span data-ttu-id="7bddb-244">서버가이 간격 내에 메시지를 보내지 않은 경우 클라이언트는 서버 연결을 끊고 `Closed` 이벤트 (JavaScript의`onclose`)를 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-244">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="7bddb-245">이 값은 ping 메시지 **를 서버에서 보내고 시간** 제한 간격 내에 클라이언트에서 받을 수 있도록 충분히 커야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-245">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="7bddb-246">권장 값은 ping이 도착할 때까지 시간을 허용 하기 위해 서버의 `KeepAliveInterval` 값을 두 번 이상 사용 하는 숫자입니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-246">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="7bddb-247">15초</span><span class="sxs-lookup"><span data-stu-id="7bddb-247">15 seconds</span></span> | <span data-ttu-id="7bddb-248">초기 서버 핸드셰이크에 대한 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-248">Timeout for initial server handshake.</span></span> <span data-ttu-id="7bddb-249">서버에서이 간격으로 핸드셰이크 응답을 보내지 않는 경우 클라이언트는 핸드셰이크를 취소 하 고 `Closed` 이벤트 (JavaScript의`onclose`)를 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-249">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="7bddb-250">이 설정은 심각한 네트워크 지연으로 인해 핸드셰이크 시간 제한 오류가 발생하는 경우에만 수정해야 하는 고급 설정입니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-250">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="7bddb-251">핸드셰이크 프로세스에 대 한 자세한 내용은 [SignalR Hub 프로토콜 사양](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="7bddb-251">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="7bddb-252">15초</span><span class="sxs-lookup"><span data-stu-id="7bddb-252">15 seconds</span></span> | <span data-ttu-id="7bddb-253">클라이언트에서 ping 메시지를 보내는 간격을 결정 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-253">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="7bddb-254">클라이언트에서 메시지를 보내면 타이머를 간격의 시작으로 다시 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-254">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="7bddb-255">클라이언트가 서버에 설정 된 `ClientTimeoutInterval`에서 메시지를 보내지 않은 경우 서버는 클라이언트의 연결을 끊은 것으로 간주 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-255">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="7bddb-256">.NET 클라이언트에서 시간 제한 값은 `TimeSpan` 값으로 지정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-256">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="7bddb-257">JavaScript</span><span class="sxs-lookup"><span data-stu-id="7bddb-257">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="7bddb-258">옵션</span><span class="sxs-lookup"><span data-stu-id="7bddb-258">Option</span></span> | <span data-ttu-id="7bddb-259">기본값</span><span class="sxs-lookup"><span data-stu-id="7bddb-259">Default value</span></span> | <span data-ttu-id="7bddb-260">Description</span><span class="sxs-lookup"><span data-stu-id="7bddb-260">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="7bddb-261">30초(30000밀리초)</span><span class="sxs-lookup"><span data-stu-id="7bddb-261">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="7bddb-262">서버 활동 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-262">Timeout for server activity.</span></span> <span data-ttu-id="7bddb-263">서버가이 간격 내에 메시지를 보내지 않은 경우 클라이언트는 서버 연결을 끊고 `onclose` 이벤트를 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-263">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="7bddb-264">이 값은 ping 메시지 **를 서버에서 보내고 시간** 제한 간격 내에 클라이언트에서 받을 수 있도록 충분히 커야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-264">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="7bddb-265">권장 값은 ping이 도착할 때까지 시간을 허용 하기 위해 서버의 `KeepAliveInterval` 값을 두 번 이상 사용 하는 숫자입니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-265">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="7bddb-266">15 초 (15000 밀리초)</span><span class="sxs-lookup"><span data-stu-id="7bddb-266">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="7bddb-267">클라이언트에서 ping 메시지를 보내는 간격을 결정 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-267">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="7bddb-268">클라이언트에서 메시지를 보내면 타이머를 간격의 시작으로 다시 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-268">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="7bddb-269">클라이언트가 서버에 설정 된 `ClientTimeoutInterval`에서 메시지를 보내지 않은 경우 서버는 클라이언트의 연결을 끊은 것으로 간주 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-269">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="7bddb-270">Java</span><span class="sxs-lookup"><span data-stu-id="7bddb-270">Java</span></span>](#tab/java)

| <span data-ttu-id="7bddb-271">옵션</span><span class="sxs-lookup"><span data-stu-id="7bddb-271">Option</span></span> | <span data-ttu-id="7bddb-272">기본값</span><span class="sxs-lookup"><span data-stu-id="7bddb-272">Default value</span></span> | <span data-ttu-id="7bddb-273">Description</span><span class="sxs-lookup"><span data-stu-id="7bddb-273">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="7bddb-274">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="7bddb-274">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="7bddb-275">30초(30000밀리초)</span><span class="sxs-lookup"><span data-stu-id="7bddb-275">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="7bddb-276">서버 활동 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-276">Timeout for server activity.</span></span> <span data-ttu-id="7bddb-277">서버가이 간격 내에 메시지를 보내지 않은 경우 클라이언트는 서버 연결을 끊고 `onClose` 이벤트를 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-277">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="7bddb-278">이 값은 ping 메시지 **를 서버에서 보내고 시간** 제한 간격 내에 클라이언트에서 받을 수 있도록 충분히 커야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-278">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="7bddb-279">권장 값은 ping이 도착할 때까지 시간을 허용 하기 위해 서버의 `KeepAliveInterval` 값을 두 번 이상 사용 하는 숫자입니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-279">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="7bddb-280">15초</span><span class="sxs-lookup"><span data-stu-id="7bddb-280">15 seconds</span></span> | <span data-ttu-id="7bddb-281">초기 서버 핸드셰이크에 대한 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-281">Timeout for initial server handshake.</span></span> <span data-ttu-id="7bddb-282">서버가이 간격 내에 핸드셰이크 응답을 보내지 않는 경우 클라이언트는 핸드셰이크를 취소 하 고 `onClose` 이벤트를 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-282">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="7bddb-283">이 설정은 심각한 네트워크 지연으로 인해 핸드셰이크 시간 제한 오류가 발생하는 경우에만 수정해야 하는 고급 설정입니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-283">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="7bddb-284">핸드셰이크 프로세스에 대 한 자세한 내용은 [SignalR Hub 프로토콜 사양](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="7bddb-284">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="7bddb-285">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="7bddb-285">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="7bddb-286">15 초 (15000 밀리초)</span><span class="sxs-lookup"><span data-stu-id="7bddb-286">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="7bddb-287">클라이언트에서 ping 메시지를 보내는 간격을 결정 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-287">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="7bddb-288">클라이언트에서 메시지를 보내면 타이머를 간격의 시작으로 다시 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-288">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="7bddb-289">클라이언트가 서버에 설정 된 `ClientTimeoutInterval`에서 메시지를 보내지 않은 경우 서버는 클라이언트의 연결을 끊은 것으로 간주 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-289">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="7bddb-290">추가 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="7bddb-290">Configure additional options</span></span>

<span data-ttu-id="7bddb-291">추가 옵션은 Java 클라이언트의 `HttpHubConnectionBuilder`에서 `HubConnectionBuilder` 또는 다양 한 구성 Api에 대 한 `WithUrl` (JavaScript의`withUrl`) 방법으로 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-291">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="7bddb-292">.NET</span><span class="sxs-lookup"><span data-stu-id="7bddb-292">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="7bddb-293">.NET 옵션</span><span class="sxs-lookup"><span data-stu-id="7bddb-293">.NET Option</span></span> |  <span data-ttu-id="7bddb-294">기본값</span><span class="sxs-lookup"><span data-stu-id="7bddb-294">Default value</span></span> | <span data-ttu-id="7bddb-295">Description</span><span class="sxs-lookup"><span data-stu-id="7bddb-295">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="7bddb-296">HTTP 요청에서 전달자 인증 토큰으로 제공된 문자열을 반환하는 함수입니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-296">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="7bddb-297">협상 단계를 건너뛰려면 `true`로 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-297">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="7bddb-298">**Websocket 전송이 유일 하 게 설정 된 전송 인 경우에만 지원 됩니다**.</span><span class="sxs-lookup"><span data-stu-id="7bddb-298">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="7bddb-299">Azure SignalR Service를 사용하는 경우에는 이 설정을 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-299">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="7bddb-300">Empty</span><span class="sxs-lookup"><span data-stu-id="7bddb-300">Empty</span></span> | <span data-ttu-id="7bddb-301">요청을 인증하기 위해 전송할 TLS 인증서의 컬렉션입니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-301">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="7bddb-302">Empty</span><span class="sxs-lookup"><span data-stu-id="7bddb-302">Empty</span></span> | <span data-ttu-id="7bddb-303">모든 HTTP 요청과 함께 전송할 HTTP 쿠키의 컬렉션입니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-303">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="7bddb-304">Empty</span><span class="sxs-lookup"><span data-stu-id="7bddb-304">Empty</span></span> | <span data-ttu-id="7bddb-305">모든 HTTP 요청과 함께 전송할 자격 증명입니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-305">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="7bddb-306">5초</span><span class="sxs-lookup"><span data-stu-id="7bddb-306">5 seconds</span></span> | <span data-ttu-id="7bddb-307">Websocket에만 해당됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-307">WebSockets only.</span></span> <span data-ttu-id="7bddb-308">클라이언트가 서버를 닫은 후 닫기 요청을 확인하기 위해 대기하는 최대 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-308">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="7bddb-309">서버가 이 시간 내에 종료를 승인하지 않으면 클라이언트가 연결을 끊습니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-309">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="7bddb-310">Empty</span><span class="sxs-lookup"><span data-stu-id="7bddb-310">Empty</span></span> | <span data-ttu-id="7bddb-311">모든 HTTP 요청과 함께 보낼 추가 HTTP 헤더의 맵입니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-311">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="7bddb-312">HTTP 요청을 보내는 데 사용 되는 `HttpMessageHandler`를 구성 하거나 대체 하는 데 사용할 수 있는 대리자입니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-312">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="7bddb-313">WebSocket 연결에는 사용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-313">Not used for WebSocket connections.</span></span> <span data-ttu-id="7bddb-314">이 대리자는 null이 아닌 값을 반환해야 하며 매개 변수로 기본값을 받습니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-314">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="7bddb-315">해당 기본값에 대 한 설정을 수정 하 고 반환 하거나 새 `HttpMessageHandler` 인스턴스를 반환 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-315">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="7bddb-316">**처리기를 대체할 때 제공 된 처리기에서 유지할 설정을 복사 해야 합니다. 그렇지 않으면 구성 된 옵션 (예: 쿠키 및 헤더)이 새 처리기에 적용 되지 않습니다.**</span><span class="sxs-lookup"><span data-stu-id="7bddb-316">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="7bddb-317">HTTP 요청을 전송할 때 사용할 HTTP 프록시입니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-317">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="7bddb-318">HTTP 및 WebSockets 요청에 대한 기본 자격 증명을 전송하려면 이 부울 값을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-318">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="7bddb-319">그러면 Windows 인증을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-319">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="7bddb-320">추가적인 WebSocket 옵션을 구성할 수 있는 대리자입니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-320">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="7bddb-321">옵션을 구성 하는 데 사용할 수 있는 [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) 의 인스턴스를 수신 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-321">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="7bddb-322">JavaScript</span><span class="sxs-lookup"><span data-stu-id="7bddb-322">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="7bddb-323">JavaScript 옵션</span><span class="sxs-lookup"><span data-stu-id="7bddb-323">JavaScript Option</span></span> | <span data-ttu-id="7bddb-324">기본값</span><span class="sxs-lookup"><span data-stu-id="7bddb-324">Default Value</span></span> | <span data-ttu-id="7bddb-325">Description</span><span class="sxs-lookup"><span data-stu-id="7bddb-325">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="7bddb-326">HTTP 요청에서 전달자 인증 토큰으로 제공된 문자열을 반환하는 함수입니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-326">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="7bddb-327">협상 단계를 건너뛰려면 `true`로 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-327">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="7bddb-328">**Websocket 전송이 유일 하 게 설정 된 전송 인 경우에만 지원 됩니다**.</span><span class="sxs-lookup"><span data-stu-id="7bddb-328">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="7bddb-329">Azure SignalR Service를 사용하는 경우에는 이 설정을 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-329">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="7bddb-330">Java</span><span class="sxs-lookup"><span data-stu-id="7bddb-330">Java</span></span>](#tab/java)

| <span data-ttu-id="7bddb-331">Java 옵션</span><span class="sxs-lookup"><span data-stu-id="7bddb-331">Java Option</span></span> | <span data-ttu-id="7bddb-332">기본값</span><span class="sxs-lookup"><span data-stu-id="7bddb-332">Default Value</span></span> | <span data-ttu-id="7bddb-333">Description</span><span class="sxs-lookup"><span data-stu-id="7bddb-333">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="7bddb-334">HTTP 요청에서 전달자 인증 토큰으로 제공된 문자열을 반환하는 함수입니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-334">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="7bddb-335">협상 단계를 건너뛰려면 `true`로 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-335">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="7bddb-336">**Websocket 전송이 유일 하 게 설정 된 전송 인 경우에만 지원 됩니다**.</span><span class="sxs-lookup"><span data-stu-id="7bddb-336">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="7bddb-337">Azure SignalR Service를 사용하는 경우에는 이 설정을 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-337">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="7bddb-338">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="7bddb-338">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="7bddb-339">Empty</span><span class="sxs-lookup"><span data-stu-id="7bddb-339">Empty</span></span> | <span data-ttu-id="7bddb-340">모든 HTTP 요청과 함께 보낼 추가 HTTP 헤더의 맵입니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-340">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="7bddb-341">.NET 클라이언트에서 이러한 옵션은 `WithUrl`에 제공 된 옵션 대리자로 수정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-341">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="7bddb-342">JavaScript 클라이언트에서 이러한 옵션은 `withUrl`에 제공 된 JavaScript 개체에 제공 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-342">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="7bddb-343">Java 클라이언트에서 이러한 옵션은에서 반환 되는 `HttpHubConnectionBuilder`에 대 한 메서드로 구성할 수 있습니다 `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="7bddb-343">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="7bddb-344">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="7bddb-344">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="7bddb-345">JSON/MessagePack serialization 옵션</span><span class="sxs-lookup"><span data-stu-id="7bddb-345">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="7bddb-346">ASP.NET Core SignalR는 메시지 인코딩에 사용할 두 가지 프로토콜인 [JSON](https://www.json.org/) 및 [MessagePack](https://msgpack.org/index.html)를 지원 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-346">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="7bddb-347">각 프로토콜에는 serialization 구성 옵션이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-347">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="7bddb-348">[AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) 확장 메서드를 사용 하 여 서버에서 JSON serialization을 구성할 수 있습니다 .이 메서드는 `Startup.ConfigureServices` 메서드에서 [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) 뒤에 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-348">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="7bddb-349">`AddJsonProtocol` 메서드는 `options` 개체를 받는 대리자를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-349">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="7bddb-350">해당 개체의 [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) 속성은 인수 및 반환 값의 serialization을 구성 하는 데 사용할 수 있는 JSON.NET `JsonSerializerSettings` 개체입니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-350">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="7bddb-351">자세한 내용은 [JSON.NET 설명서](https://www.newtonsoft.com/json/help/html/Introduction.htm)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="7bddb-351">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="7bddb-352">예를 들어 기본 "camelCase" 이름 대신 "대/소문자 구분" 속성 이름을 사용 하도록 serializer를 구성 하려면 `Startup.ConfigureServices`에서 다음 코드를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-352">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="7bddb-353">.NET 클라이언트에서는 [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)에 동일한 `AddJsonProtocol` 확장 메서드가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-353">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="7bddb-354">확장 메서드를 확인 하려면 `Microsoft.Extensions.DependencyInjection` 네임 스페이스를 가져와야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-354">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="7bddb-355">현재 JavaScript 클라이언트에서는 JSON 직렬화를 구성할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-355">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="7bddb-356">MessagePack serialization 옵션</span><span class="sxs-lookup"><span data-stu-id="7bddb-356">MessagePack serialization options</span></span>

<span data-ttu-id="7bddb-357">MessagePack serialization은 [Addmessagepackprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) 호출에 대리자를 제공 하 여 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-357">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="7bddb-358">자세한 내용은 [MessagePack In SignalR](xref:signalr/messagepackhubprotocol) 을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="7bddb-358">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="7bddb-359">현재 JavaScript 클라이언트에서는 MessagePack 직렬화를 구성할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-359">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="7bddb-360">서버 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="7bddb-360">Configure server options</span></span>

<span data-ttu-id="7bddb-361">다음 표는 SignalR 허브를 구성하기 위한 옵션을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-361">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="7bddb-362">옵션</span><span class="sxs-lookup"><span data-stu-id="7bddb-362">Option</span></span> | <span data-ttu-id="7bddb-363">기본값</span><span class="sxs-lookup"><span data-stu-id="7bddb-363">Default Value</span></span> | <span data-ttu-id="7bddb-364">Description</span><span class="sxs-lookup"><span data-stu-id="7bddb-364">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="7bddb-365">30초</span><span class="sxs-lookup"><span data-stu-id="7bddb-365">30 seconds</span></span> | <span data-ttu-id="7bddb-366">서버는이 간격 내에 메시지를 받지 못한 경우 (keep-alive 포함) 클라이언트의 연결을 해제 하는 것으로 간주 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-366">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="7bddb-367">이를 구현 하는 방법으로 인해 클라이언트에서 실제로 연결이 끊어진 것으로 표시 되는 시간 제한 간격 보다 오래 걸릴 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-367">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="7bddb-368">권장 값은 두 개의 `KeepAliveInterval` 값입니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-368">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="7bddb-369">15초</span><span class="sxs-lookup"><span data-stu-id="7bddb-369">15 seconds</span></span> | <span data-ttu-id="7bddb-370">클라이언트가 이 시간 제한 내에 초기 핸드셰이크 메시지를 전송하지 않으면 연결이 닫힙니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-370">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="7bddb-371">이 설정은 심각한 네트워크 지연으로 인해 핸드셰이크 시간 제한 오류가 발생하는 경우에만 수정해야 하는 고급 설정입니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-371">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="7bddb-372">핸드셰이크 프로세스에 대 한 자세한 내용은 [SignalR Hub 프로토콜 사양](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="7bddb-372">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="7bddb-373">15초</span><span class="sxs-lookup"><span data-stu-id="7bddb-373">15 seconds</span></span> | <span data-ttu-id="7bddb-374">서버가 이 간격 내에 메시지를 전송하지 않으면 자동으로 ping 메시지가 전송되어 연결이 열린 상태로 유지됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-374">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="7bddb-375">`KeepAliveInterval`를 변경 하는 경우 클라이언트에서 `ServerTimeout`/`serverTimeoutInMilliseconds` 설정을 변경 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-375">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="7bddb-376">권장 되는 `ServerTimeout`/`serverTimeoutInMilliseconds` 값은 `KeepAliveInterval` 값 두 배가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-376">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="7bddb-377">설치된 모든 프로토콜</span><span class="sxs-lookup"><span data-stu-id="7bddb-377">All installed protocols</span></span> | <span data-ttu-id="7bddb-378">허브가 지원하는 프로토콜입니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-378">Protocols supported by this hub.</span></span> <span data-ttu-id="7bddb-379">기본적으로 서버에 등록된 모든 프로토콜이 허용되지만 이 목록에서 프로토콜을 제거하여 개별 허브에 대해 특정 프로토콜을 비활성화시킬 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-379">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="7bddb-380">`true`경우 허브 메서드에서 예외가 throw 되 면 자세한 예외 메시지가 클라이언트에 반환 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-380">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="7bddb-381">이러한 예외 메시지에는 중요 한 정보가 포함 될 수 있으므로 기본값은 `false`입니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-381">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="7bddb-382">`Startup.ConfigureServices`에서 `AddSignalR` 호출에 옵션 대리자를 제공 하 여 모든 허브에 대 한 옵션을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-382">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="7bddb-383">단일 허브에 대 한 옵션은 `AddSignalR`에서 제공 하는 전역 옵션을 재정의 하며 <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>를 사용 하 여 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-383">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="7bddb-384">고급 HTTP 구성 옵션</span><span class="sxs-lookup"><span data-stu-id="7bddb-384">Advanced HTTP configuration options</span></span>

<span data-ttu-id="7bddb-385">`HttpConnectionDispatcherOptions`를 사용 하 여 전송 및 메모리 버퍼 관리와 관련 된 고급 설정을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-385">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="7bddb-386">이러한 옵션은 `Startup.Configure`에서 [\<t >의 Maphub](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) 에 대리자를 전달 하 여 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-386">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="7bddb-387">다음 표에서는 ASP.NET Core SignalR의 고급 HTTP 옵션을 구성 하는 옵션을 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-387">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="7bddb-388">옵션</span><span class="sxs-lookup"><span data-stu-id="7bddb-388">Option</span></span> | <span data-ttu-id="7bddb-389">기본값</span><span class="sxs-lookup"><span data-stu-id="7bddb-389">Default Value</span></span> | <span data-ttu-id="7bddb-390">Description</span><span class="sxs-lookup"><span data-stu-id="7bddb-390">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="7bddb-391">32KB</span><span class="sxs-lookup"><span data-stu-id="7bddb-391">32 KB</span></span> | <span data-ttu-id="7bddb-392">클라이언트로부터 수신하는 서버 버퍼의 최대 바이트 수입니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-392">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="7bddb-393">이 값을 늘리면 서버가 더 큰 메시지를 수신할 수 있지만 메모리 소비에 부정적인 영향을 줄 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-393">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="7bddb-394">허브 클래스에 적용 된 `Authorize` 특성에서 자동으로 수집 된 데이터입니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-394">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="7bddb-395">클라이언트에 허브에 연결할 수 있는 권한이 있는지 여부를 확인 하는 데 사용 되는 [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) 개체의 목록입니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-395">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="7bddb-396">32KB</span><span class="sxs-lookup"><span data-stu-id="7bddb-396">32 KB</span></span> | <span data-ttu-id="7bddb-397">앱에서 전송하는 서버 버퍼의 최대 바이트 수입니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-397">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="7bddb-398">이 값을 늘리면 서버가 더 큰 메시지를 전송할 수 있지만 메모리 소비에 부정적인 영향을 줄 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-398">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="7bddb-399">모든 전송을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-399">All Transports are enabled.</span></span> | <span data-ttu-id="7bddb-400">클라이언트에서 연결 하는 데 사용할 수 있는 전송을 제한할 수 있는 `HttpTransportType` 값의 비트 플래그 열거형입니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-400">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="7bddb-401">아래를 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="7bddb-401">See below.</span></span> | <span data-ttu-id="7bddb-402">롱 폴링 전송과 관련된 추가 옵션입니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-402">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="7bddb-403">아래를 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="7bddb-403">See below.</span></span> | <span data-ttu-id="7bddb-404">WebSockets 전송과 관련된 추가 옵션입니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-404">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="7bddb-405">긴 폴링 전송에는 `LongPolling` 속성을 사용 하 여 구성할 수 있는 추가 옵션이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-405">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="7bddb-406">옵션</span><span class="sxs-lookup"><span data-stu-id="7bddb-406">Option</span></span> | <span data-ttu-id="7bddb-407">기본값</span><span class="sxs-lookup"><span data-stu-id="7bddb-407">Default Value</span></span> | <span data-ttu-id="7bddb-408">Description</span><span class="sxs-lookup"><span data-stu-id="7bddb-408">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="7bddb-409">90초</span><span class="sxs-lookup"><span data-stu-id="7bddb-409">90 seconds</span></span> | <span data-ttu-id="7bddb-410">단일 폴링 요청을 종료하기 전에 서버가 메시지를 클라이언트에 전송할 때까지 대기하는 최대 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-410">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="7bddb-411">이 값을 줄이면 클라이언트는 더 자주 새 폴링 요청을 발행합니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-411">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="7bddb-412">WebSocket 전송은 `WebSockets` 속성을 사용 하 여 구성할 수 있는 추가 옵션을 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-412">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="7bddb-413">옵션</span><span class="sxs-lookup"><span data-stu-id="7bddb-413">Option</span></span> | <span data-ttu-id="7bddb-414">기본값</span><span class="sxs-lookup"><span data-stu-id="7bddb-414">Default Value</span></span> | <span data-ttu-id="7bddb-415">Description</span><span class="sxs-lookup"><span data-stu-id="7bddb-415">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="7bddb-416">5초</span><span class="sxs-lookup"><span data-stu-id="7bddb-416">5 seconds</span></span> | <span data-ttu-id="7bddb-417">서버가 닫힌 후 클라이언트가 이 시간 제한 내에 닫기에 실패하면 연결이 종료됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-417">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="7bddb-418">`Sec-WebSocket-Protocol` 헤더를 사용자 지정 값으로 설정 하는 데 사용할 수 있는 대리자입니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-418">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="7bddb-419">이 대리자는 클라이언트에서 요청한 값을 입력으로 받아서 원하는 값을 반환해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-419">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="7bddb-420">클라이언트 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="7bddb-420">Configure client options</span></span>

<span data-ttu-id="7bddb-421">클라이언트 옵션은 `HubConnectionBuilder` 형식 (.NET 및 JavaScript 클라이언트에서 사용 가능)에서 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-421">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="7bddb-422">Java 클라이언트 에서도 사용할 수 있지만 `HttpHubConnectionBuilder` 하위 클래스는 작성기 구성 옵션과 `HubConnection` 자체를 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-422">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="7bddb-423">로깅 구성</span><span class="sxs-lookup"><span data-stu-id="7bddb-423">Configure logging</span></span>

<span data-ttu-id="7bddb-424">로깅은 `ConfigureLogging` 메서드를 사용 하 여 .NET 클라이언트에서 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-424">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="7bddb-425">로깅 공급자 및 필터는 서버와 동일한 방법으로 등록할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-425">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="7bddb-426">자세한 내용은 [ASP.NET Core의 로깅](xref:fundamentals/logging/index) 문서를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="7bddb-426">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="7bddb-427">로깅 공급자를 등록하는 데 필요한 패키지를 설치해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-427">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="7bddb-428">전체 목록은 문서에서 [기본 제공 로깅 공급자](xref:fundamentals/logging/index#built-in-logging-providers) 섹션을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="7bddb-428">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="7bddb-429">예를 들어 콘솔 로깅을 사용 하도록 설정 하려면 `Microsoft.Extensions.Logging.Console` NuGet 패키지를 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-429">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="7bddb-430">`AddConsole` 확장 메서드를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-430">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="7bddb-431">JavaScript 클라이언트에서 유사한 `configureLogging` 메서드가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-431">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="7bddb-432">생성할 로그 메시지의 최소 수준을 나타내는 `LogLevel` 값을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-432">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="7bddb-433">로그는 브라우저 콘솔 창에 기록됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-433">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="7bddb-434">로깅을 완전히 사용 하지 않도록 설정 하려면 `configureLogging` 메서드에서 `signalR.LogLevel.None`를 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-434">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="7bddb-435">로깅에 대 한 자세한 내용은 [SignalR 진단 설명서](xref:signalr/diagnostics)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="7bddb-435">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="7bddb-436">SignalR Java 클라이언트는 [SLF4J](https://www.slf4j.org/) 라이브러리를 사용 하 여 로깅을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-436">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="7bddb-437">이는 라이브러리의 사용자가 특정 로깅 종속성을 가져와 자신의 특정 로깅 구현을 선택할 수 있게 해 주는 상위 수준 로깅 API입니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-437">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="7bddb-438">다음 코드 조각에서는 SignalR Java 클라이언트와 함께 `java.util.logging`를 사용 하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-438">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="7bddb-439">종속성에서 로깅을 구성 하지 않으면 SLF4J는 다음과 같은 경고 메시지와 함께 기본 작업 없음로 거를 로드 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-439">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="7bddb-440">이는 무시 해도 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-440">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="7bddb-441">허용되는 전송 구성</span><span class="sxs-lookup"><span data-stu-id="7bddb-441">Configure allowed transports</span></span>

<span data-ttu-id="7bddb-442">SignalR에서 사용 하는 전송은 `WithUrl` 호출 (JavaScript의`withUrl`)에서 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-442">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="7bddb-443">`HttpTransportType` 값의 비트 or를 사용 하 여 지정 된 전송만 사용 하도록 클라이언트를 제한할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-443">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="7bddb-444">기본적으로 모든 전송은 활성화되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-444">All transports are enabled by default.</span></span>

<span data-ttu-id="7bddb-445">예를 들어 다음 코드는 서버-전송 이벤트 전송을 비활성화시키고 WebSocket 및 롱 폴링 연결만 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-445">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="7bddb-446">JavaScript 클라이언트에서 `withUrl`에 제공 된 options 개체의 `transport` 필드를 설정 하 여 전송이 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-446">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="7bddb-447">이 버전의 Java 클라이언트 websocket은 유일 하 게 사용할 수 있는 전송입니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-447">In this version of the Java client websockets is the only available transport.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="7bddb-448">전달자 인증 구성</span><span class="sxs-lookup"><span data-stu-id="7bddb-448">Configure bearer authentication</span></span>

<span data-ttu-id="7bddb-449">SignalR 요청과 함께 인증 데이터를 제공 하려면 `AccessTokenProvider` 옵션 (JavaScript에서`accessTokenFactory`)을 사용 하 여 원하는 액세스 토큰을 반환 하는 함수를 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-449">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="7bddb-450">.NET 클라이언트에서이 액세스 토큰은 HTTP "전달자 인증" 토큰으로 전달 됩니다 (`Bearer`형식으로 `Authorization` 헤더 사용).</span><span class="sxs-lookup"><span data-stu-id="7bddb-450">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="7bddb-451">JavaScript 클라이언트에서 액세스 토큰은 브라우저 Api가 헤더를 적용 하는 기능을 제한 하는 경우를 **제외** 하 고 전달자 토큰으로 사용 됩니다 (특히 서버에서 보낸 이벤트와 websocket 요청에서).</span><span class="sxs-lookup"><span data-stu-id="7bddb-451">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="7bddb-452">이러한 경우 액세스 토큰은 `access_token`쿼리 문자열 값으로 제공 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-452">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="7bddb-453">.NET 클라이언트에서 `AccessTokenProvider` 옵션은 `WithUrl`의 옵션 대리자를 사용 하 여 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-453">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="7bddb-454">JavaScript 클라이언트에서 액세스 토큰은 `withUrl`의 options 개체에 `accessTokenFactory` 필드를 설정 하 여 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-454">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="7bddb-455">SignalR Java 클라이언트에서 [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)에 액세스 토큰 팩터리를 제공 하 여 인증에 사용할 전달자 토큰을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-455">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="7bddb-456">[WithAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) 를 사용 하 여 [rxjava](https://github.com/ReactiveX/RxJava) [Single\<문자열 >](https://reactivex.io/documentation/single.html)를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-456">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="7bddb-457">[단일. 지연](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)에 대 한 호출을 사용 하 여 클라이언트에 대 한 액세스 토큰을 생성 하는 논리를 작성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-457">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="7bddb-458">시간 제한 및 연결 유지 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="7bddb-458">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="7bddb-459">제한 시간 및 연결 유지 동작을 구성 하기 위한 추가 옵션은 `HubConnection` 개체 자체에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-459">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="7bddb-460">.NET</span><span class="sxs-lookup"><span data-stu-id="7bddb-460">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="7bddb-461">옵션</span><span class="sxs-lookup"><span data-stu-id="7bddb-461">Option</span></span> | <span data-ttu-id="7bddb-462">기본값</span><span class="sxs-lookup"><span data-stu-id="7bddb-462">Default value</span></span> | <span data-ttu-id="7bddb-463">Description</span><span class="sxs-lookup"><span data-stu-id="7bddb-463">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="7bddb-464">30초(30000밀리초)</span><span class="sxs-lookup"><span data-stu-id="7bddb-464">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="7bddb-465">서버 활동 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-465">Timeout for server activity.</span></span> <span data-ttu-id="7bddb-466">서버가이 간격 내에 메시지를 보내지 않은 경우 클라이언트는 서버 연결을 끊고 `Closed` 이벤트 (JavaScript의`onclose`)를 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-466">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="7bddb-467">이 값은 ping 메시지 **를 서버에서 보내고 시간** 제한 간격 내에 클라이언트에서 받을 수 있도록 충분히 커야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-467">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="7bddb-468">권장 값은 ping이 도착할 때까지 시간을 허용 하기 위해 서버의 `KeepAliveInterval` 값을 두 번 이상 사용 하는 숫자입니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-468">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="7bddb-469">15초</span><span class="sxs-lookup"><span data-stu-id="7bddb-469">15 seconds</span></span> | <span data-ttu-id="7bddb-470">초기 서버 핸드셰이크에 대한 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-470">Timeout for initial server handshake.</span></span> <span data-ttu-id="7bddb-471">서버에서이 간격으로 핸드셰이크 응답을 보내지 않는 경우 클라이언트는 핸드셰이크를 취소 하 고 `Closed` 이벤트 (JavaScript의`onclose`)를 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-471">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="7bddb-472">이 설정은 심각한 네트워크 지연으로 인해 핸드셰이크 시간 제한 오류가 발생하는 경우에만 수정해야 하는 고급 설정입니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-472">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="7bddb-473">핸드셰이크 프로세스에 대 한 자세한 내용은 [SignalR Hub 프로토콜 사양](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="7bddb-473">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="7bddb-474">15초</span><span class="sxs-lookup"><span data-stu-id="7bddb-474">15 seconds</span></span> | <span data-ttu-id="7bddb-475">클라이언트에서 ping 메시지를 보내는 간격을 결정 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-475">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="7bddb-476">클라이언트에서 메시지를 보내면 타이머를 간격의 시작으로 다시 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-476">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="7bddb-477">클라이언트가 서버에 설정 된 `ClientTimeoutInterval`에서 메시지를 보내지 않은 경우 서버는 클라이언트의 연결을 끊은 것으로 간주 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-477">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="7bddb-478">.NET 클라이언트에서 시간 제한 값은 `TimeSpan` 값으로 지정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-478">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="7bddb-479">JavaScript</span><span class="sxs-lookup"><span data-stu-id="7bddb-479">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="7bddb-480">옵션</span><span class="sxs-lookup"><span data-stu-id="7bddb-480">Option</span></span> | <span data-ttu-id="7bddb-481">기본값</span><span class="sxs-lookup"><span data-stu-id="7bddb-481">Default value</span></span> | <span data-ttu-id="7bddb-482">Description</span><span class="sxs-lookup"><span data-stu-id="7bddb-482">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="7bddb-483">30초(30000밀리초)</span><span class="sxs-lookup"><span data-stu-id="7bddb-483">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="7bddb-484">서버 활동 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-484">Timeout for server activity.</span></span> <span data-ttu-id="7bddb-485">서버가이 간격 내에 메시지를 보내지 않은 경우 클라이언트는 서버 연결을 끊고 `onclose` 이벤트를 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-485">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="7bddb-486">이 값은 ping 메시지 **를 서버에서 보내고 시간** 제한 간격 내에 클라이언트에서 받을 수 있도록 충분히 커야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-486">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="7bddb-487">권장 값은 ping이 도착할 때까지 시간을 허용 하기 위해 서버의 `KeepAliveInterval` 값을 두 번 이상 사용 하는 숫자입니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-487">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="7bddb-488">15 초 (15000 밀리초)</span><span class="sxs-lookup"><span data-stu-id="7bddb-488">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="7bddb-489">클라이언트에서 ping 메시지를 보내는 간격을 결정 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-489">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="7bddb-490">클라이언트에서 메시지를 보내면 타이머를 간격의 시작으로 다시 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-490">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="7bddb-491">클라이언트가 서버에 설정 된 `ClientTimeoutInterval`에서 메시지를 보내지 않은 경우 서버는 클라이언트의 연결을 끊은 것으로 간주 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-491">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="7bddb-492">Java</span><span class="sxs-lookup"><span data-stu-id="7bddb-492">Java</span></span>](#tab/java)

| <span data-ttu-id="7bddb-493">옵션</span><span class="sxs-lookup"><span data-stu-id="7bddb-493">Option</span></span> | <span data-ttu-id="7bddb-494">기본값</span><span class="sxs-lookup"><span data-stu-id="7bddb-494">Default value</span></span> | <span data-ttu-id="7bddb-495">Description</span><span class="sxs-lookup"><span data-stu-id="7bddb-495">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="7bddb-496">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="7bddb-496">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="7bddb-497">30초(30000밀리초)</span><span class="sxs-lookup"><span data-stu-id="7bddb-497">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="7bddb-498">서버 활동 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-498">Timeout for server activity.</span></span> <span data-ttu-id="7bddb-499">서버가이 간격 내에 메시지를 보내지 않은 경우 클라이언트는 서버 연결을 끊고 `onClose` 이벤트를 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-499">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="7bddb-500">이 값은 ping 메시지 **를 서버에서 보내고 시간** 제한 간격 내에 클라이언트에서 받을 수 있도록 충분히 커야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-500">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="7bddb-501">권장 값은 ping이 도착할 때까지 시간을 허용 하기 위해 서버의 `KeepAliveInterval` 값을 두 번 이상 사용 하는 숫자입니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-501">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="7bddb-502">15초</span><span class="sxs-lookup"><span data-stu-id="7bddb-502">15 seconds</span></span> | <span data-ttu-id="7bddb-503">초기 서버 핸드셰이크에 대한 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-503">Timeout for initial server handshake.</span></span> <span data-ttu-id="7bddb-504">서버가이 간격 내에 핸드셰이크 응답을 보내지 않는 경우 클라이언트는 핸드셰이크를 취소 하 고 `onClose` 이벤트를 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-504">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="7bddb-505">이 설정은 심각한 네트워크 지연으로 인해 핸드셰이크 시간 제한 오류가 발생하는 경우에만 수정해야 하는 고급 설정입니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-505">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="7bddb-506">핸드셰이크 프로세스에 대 한 자세한 내용은 [SignalR Hub 프로토콜 사양](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="7bddb-506">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="7bddb-507">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="7bddb-507">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="7bddb-508">15 초 (15000 밀리초)</span><span class="sxs-lookup"><span data-stu-id="7bddb-508">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="7bddb-509">클라이언트에서 ping 메시지를 보내는 간격을 결정 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-509">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="7bddb-510">클라이언트에서 메시지를 보내면 타이머를 간격의 시작으로 다시 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-510">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="7bddb-511">클라이언트가 서버에 설정 된 `ClientTimeoutInterval`에서 메시지를 보내지 않은 경우 서버는 클라이언트의 연결을 끊은 것으로 간주 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-511">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="7bddb-512">추가 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="7bddb-512">Configure additional options</span></span>

<span data-ttu-id="7bddb-513">추가 옵션은 Java 클라이언트의 `HttpHubConnectionBuilder`에서 `HubConnectionBuilder` 또는 다양 한 구성 Api에 대 한 `WithUrl` (JavaScript의`withUrl`) 방법으로 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-513">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="7bddb-514">.NET</span><span class="sxs-lookup"><span data-stu-id="7bddb-514">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="7bddb-515">.NET 옵션</span><span class="sxs-lookup"><span data-stu-id="7bddb-515">.NET Option</span></span> |  <span data-ttu-id="7bddb-516">기본값</span><span class="sxs-lookup"><span data-stu-id="7bddb-516">Default value</span></span> | <span data-ttu-id="7bddb-517">Description</span><span class="sxs-lookup"><span data-stu-id="7bddb-517">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="7bddb-518">HTTP 요청에서 전달자 인증 토큰으로 제공된 문자열을 반환하는 함수입니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-518">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="7bddb-519">협상 단계를 건너뛰려면 `true`로 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-519">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="7bddb-520">**Websocket 전송이 유일 하 게 설정 된 전송 인 경우에만 지원 됩니다**.</span><span class="sxs-lookup"><span data-stu-id="7bddb-520">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="7bddb-521">Azure SignalR Service를 사용하는 경우에는 이 설정을 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-521">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="7bddb-522">Empty</span><span class="sxs-lookup"><span data-stu-id="7bddb-522">Empty</span></span> | <span data-ttu-id="7bddb-523">요청을 인증하기 위해 전송할 TLS 인증서의 컬렉션입니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-523">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="7bddb-524">Empty</span><span class="sxs-lookup"><span data-stu-id="7bddb-524">Empty</span></span> | <span data-ttu-id="7bddb-525">모든 HTTP 요청과 함께 전송할 HTTP 쿠키의 컬렉션입니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-525">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="7bddb-526">Empty</span><span class="sxs-lookup"><span data-stu-id="7bddb-526">Empty</span></span> | <span data-ttu-id="7bddb-527">모든 HTTP 요청과 함께 전송할 자격 증명입니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-527">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="7bddb-528">5초</span><span class="sxs-lookup"><span data-stu-id="7bddb-528">5 seconds</span></span> | <span data-ttu-id="7bddb-529">Websocket에만 해당됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-529">WebSockets only.</span></span> <span data-ttu-id="7bddb-530">클라이언트가 서버를 닫은 후 닫기 요청을 확인하기 위해 대기하는 최대 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-530">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="7bddb-531">서버가 이 시간 내에 종료를 승인하지 않으면 클라이언트가 연결을 끊습니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-531">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="7bddb-532">Empty</span><span class="sxs-lookup"><span data-stu-id="7bddb-532">Empty</span></span> | <span data-ttu-id="7bddb-533">모든 HTTP 요청과 함께 보낼 추가 HTTP 헤더의 맵입니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-533">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="7bddb-534">HTTP 요청을 보내는 데 사용 되는 `HttpMessageHandler`를 구성 하거나 대체 하는 데 사용할 수 있는 대리자입니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-534">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="7bddb-535">WebSocket 연결에는 사용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-535">Not used for WebSocket connections.</span></span> <span data-ttu-id="7bddb-536">이 대리자는 null이 아닌 값을 반환해야 하며 매개 변수로 기본값을 받습니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-536">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="7bddb-537">해당 기본값에 대 한 설정을 수정 하 고 반환 하거나 새 `HttpMessageHandler` 인스턴스를 반환 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-537">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="7bddb-538">**처리기를 대체할 때 제공 된 처리기에서 유지할 설정을 복사 해야 합니다. 그렇지 않으면 구성 된 옵션 (예: 쿠키 및 헤더)이 새 처리기에 적용 되지 않습니다.**</span><span class="sxs-lookup"><span data-stu-id="7bddb-538">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="7bddb-539">HTTP 요청을 전송할 때 사용할 HTTP 프록시입니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-539">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="7bddb-540">HTTP 및 WebSockets 요청에 대한 기본 자격 증명을 전송하려면 이 부울 값을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-540">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="7bddb-541">그러면 Windows 인증을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-541">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="7bddb-542">추가적인 WebSocket 옵션을 구성할 수 있는 대리자입니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-542">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="7bddb-543">옵션을 구성 하는 데 사용할 수 있는 [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) 의 인스턴스를 수신 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-543">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="7bddb-544">JavaScript</span><span class="sxs-lookup"><span data-stu-id="7bddb-544">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="7bddb-545">JavaScript 옵션</span><span class="sxs-lookup"><span data-stu-id="7bddb-545">JavaScript Option</span></span> | <span data-ttu-id="7bddb-546">기본값</span><span class="sxs-lookup"><span data-stu-id="7bddb-546">Default Value</span></span> | <span data-ttu-id="7bddb-547">Description</span><span class="sxs-lookup"><span data-stu-id="7bddb-547">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="7bddb-548">HTTP 요청에서 전달자 인증 토큰으로 제공된 문자열을 반환하는 함수입니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-548">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="7bddb-549">협상 단계를 건너뛰려면 `true`로 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-549">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="7bddb-550">**Websocket 전송이 유일 하 게 설정 된 전송 인 경우에만 지원 됩니다**.</span><span class="sxs-lookup"><span data-stu-id="7bddb-550">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="7bddb-551">Azure SignalR Service를 사용하는 경우에는 이 설정을 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-551">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="7bddb-552">Java</span><span class="sxs-lookup"><span data-stu-id="7bddb-552">Java</span></span>](#tab/java)

| <span data-ttu-id="7bddb-553">Java 옵션</span><span class="sxs-lookup"><span data-stu-id="7bddb-553">Java Option</span></span> | <span data-ttu-id="7bddb-554">기본값</span><span class="sxs-lookup"><span data-stu-id="7bddb-554">Default Value</span></span> | <span data-ttu-id="7bddb-555">Description</span><span class="sxs-lookup"><span data-stu-id="7bddb-555">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="7bddb-556">HTTP 요청에서 전달자 인증 토큰으로 제공된 문자열을 반환하는 함수입니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-556">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="7bddb-557">협상 단계를 건너뛰려면 `true`로 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-557">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="7bddb-558">**Websocket 전송이 유일 하 게 설정 된 전송 인 경우에만 지원 됩니다**.</span><span class="sxs-lookup"><span data-stu-id="7bddb-558">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="7bddb-559">Azure SignalR Service를 사용하는 경우에는 이 설정을 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-559">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="7bddb-560">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="7bddb-560">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="7bddb-561">Empty</span><span class="sxs-lookup"><span data-stu-id="7bddb-561">Empty</span></span> | <span data-ttu-id="7bddb-562">모든 HTTP 요청과 함께 보낼 추가 HTTP 헤더의 맵입니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-562">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="7bddb-563">.NET 클라이언트에서 이러한 옵션은 `WithUrl`에 제공 된 옵션 대리자로 수정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-563">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="7bddb-564">JavaScript 클라이언트에서 이러한 옵션은 `withUrl`에 제공 된 JavaScript 개체에 제공 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-564">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="7bddb-565">Java 클라이언트에서 이러한 옵션은에서 반환 되는 `HttpHubConnectionBuilder`에 대 한 메서드로 구성할 수 있습니다 `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="7bddb-565">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="7bddb-566">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="7bddb-566">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="< aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="7bddb-567">JSON/MessagePack serialization 옵션</span><span class="sxs-lookup"><span data-stu-id="7bddb-567">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="7bddb-568">ASP.NET Core SignalR는 메시지 인코딩에 사용할 두 가지 프로토콜인 [JSON](https://www.json.org/) 및 [MessagePack](https://msgpack.org/index.html)를 지원 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-568">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="7bddb-569">각 프로토콜에는 serialization 구성 옵션이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-569">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="7bddb-570">[AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) 확장 메서드를 사용 하 여 서버에서 JSON serialization을 구성할 수 있습니다 .이 메서드는 `Startup.ConfigureServices` 메서드에서 [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) 뒤에 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-570">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="7bddb-571">`AddJsonProtocol` 메서드는 `options` 개체를 받는 대리자를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-571">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="7bddb-572">해당 개체의 [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) 속성은 인수 및 반환 값의 serialization을 구성 하는 데 사용할 수 있는 JSON.NET `JsonSerializerSettings` 개체입니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-572">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="7bddb-573">자세한 내용은 [JSON.NET 설명서](https://www.newtonsoft.com/json/help/html/Introduction.htm)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="7bddb-573">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="7bddb-574">예를 들어 기본 "camelCase" 이름 대신 "대/소문자 구분" 속성 이름을 사용 하도록 serializer를 구성 하려면 `Startup.ConfigureServices`에서 다음 코드를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-574">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="7bddb-575">.NET 클라이언트에서는 [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)에 동일한 `AddJsonProtocol` 확장 메서드가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-575">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="7bddb-576">확장 메서드를 확인 하려면 `Microsoft.Extensions.DependencyInjection` 네임 스페이스를 가져와야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-576">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="7bddb-577">현재 JavaScript 클라이언트에서는 JSON 직렬화를 구성할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-577">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="7bddb-578">MessagePack serialization 옵션</span><span class="sxs-lookup"><span data-stu-id="7bddb-578">MessagePack serialization options</span></span>

<span data-ttu-id="7bddb-579">MessagePack serialization은 [Addmessagepackprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) 호출에 대리자를 제공 하 여 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-579">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="7bddb-580">자세한 내용은 [MessagePack In SignalR](xref:signalr/messagepackhubprotocol) 을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="7bddb-580">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="7bddb-581">현재 JavaScript 클라이언트에서는 MessagePack 직렬화를 구성할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-581">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="7bddb-582">서버 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="7bddb-582">Configure server options</span></span>

<span data-ttu-id="7bddb-583">다음 표는 SignalR 허브를 구성하기 위한 옵션을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-583">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="7bddb-584">옵션</span><span class="sxs-lookup"><span data-stu-id="7bddb-584">Option</span></span> | <span data-ttu-id="7bddb-585">기본값</span><span class="sxs-lookup"><span data-stu-id="7bddb-585">Default Value</span></span> | <span data-ttu-id="7bddb-586">Description</span><span class="sxs-lookup"><span data-stu-id="7bddb-586">Description</span></span> |
| ------ | ------------- | ----------- |
| `HandshakeTimeout` | <span data-ttu-id="7bddb-587">15초</span><span class="sxs-lookup"><span data-stu-id="7bddb-587">15 seconds</span></span> | <span data-ttu-id="7bddb-588">클라이언트가 이 시간 제한 내에 초기 핸드셰이크 메시지를 전송하지 않으면 연결이 닫힙니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-588">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="7bddb-589">이 설정은 심각한 네트워크 지연으로 인해 핸드셰이크 시간 제한 오류가 발생하는 경우에만 수정해야 하는 고급 설정입니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-589">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="7bddb-590">핸드셰이크 프로세스에 대 한 자세한 내용은 [SignalR Hub 프로토콜 사양](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="7bddb-590">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="7bddb-591">15초</span><span class="sxs-lookup"><span data-stu-id="7bddb-591">15 seconds</span></span> | <span data-ttu-id="7bddb-592">서버가 이 간격 내에 메시지를 전송하지 않으면 자동으로 ping 메시지가 전송되어 연결이 열린 상태로 유지됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-592">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="7bddb-593">`KeepAliveInterval`를 변경 하는 경우 클라이언트에서 `ServerTimeout`/`serverTimeoutInMilliseconds` 설정을 변경 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-593">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="7bddb-594">권장 되는 `ServerTimeout`/`serverTimeoutInMilliseconds` 값은 `KeepAliveInterval` 값 두 배가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-594">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="7bddb-595">설치된 모든 프로토콜</span><span class="sxs-lookup"><span data-stu-id="7bddb-595">All installed protocols</span></span> | <span data-ttu-id="7bddb-596">허브가 지원하는 프로토콜입니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-596">Protocols supported by this hub.</span></span> <span data-ttu-id="7bddb-597">기본적으로 서버에 등록된 모든 프로토콜이 허용되지만 이 목록에서 프로토콜을 제거하여 개별 허브에 대해 특정 프로토콜을 비활성화시킬 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-597">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="7bddb-598">`true`경우 허브 메서드에서 예외가 throw 되 면 자세한 예외 메시지가 클라이언트에 반환 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-598">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="7bddb-599">이러한 예외 메시지에는 중요 한 정보가 포함 될 수 있으므로 기본값은 `false`입니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-599">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="7bddb-600">`Startup.ConfigureServices`에서 `AddSignalR` 호출에 옵션 대리자를 제공 하 여 모든 허브에 대 한 옵션을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-600">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="7bddb-601">단일 허브에 대 한 옵션은 `AddSignalR`에서 제공 하는 전역 옵션을 재정의 하며 <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>를 사용 하 여 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-601">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="7bddb-602">고급 HTTP 구성 옵션</span><span class="sxs-lookup"><span data-stu-id="7bddb-602">Advanced HTTP configuration options</span></span>

<span data-ttu-id="7bddb-603">`HttpConnectionDispatcherOptions`를 사용 하 여 전송 및 메모리 버퍼 관리와 관련 된 고급 설정을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-603">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="7bddb-604">이러한 옵션은 `Startup.Configure`에서 [\<t >의 Maphub](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) 에 대리자를 전달 하 여 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-604">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="7bddb-605">다음 표에서는 ASP.NET Core SignalR의 고급 HTTP 옵션을 구성 하는 옵션을 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-605">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="7bddb-606">옵션</span><span class="sxs-lookup"><span data-stu-id="7bddb-606">Option</span></span> | <span data-ttu-id="7bddb-607">기본값</span><span class="sxs-lookup"><span data-stu-id="7bddb-607">Default Value</span></span> | <span data-ttu-id="7bddb-608">Description</span><span class="sxs-lookup"><span data-stu-id="7bddb-608">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="7bddb-609">32KB</span><span class="sxs-lookup"><span data-stu-id="7bddb-609">32 KB</span></span> | <span data-ttu-id="7bddb-610">클라이언트로부터 수신하는 서버 버퍼의 최대 바이트 수입니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-610">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="7bddb-611">이 값을 늘리면 서버가 더 큰 메시지를 수신할 수 있지만 메모리 소비에 부정적인 영향을 줄 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-611">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="7bddb-612">허브 클래스에 적용 된 `Authorize` 특성에서 자동으로 수집 된 데이터입니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-612">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="7bddb-613">클라이언트에 허브에 연결할 수 있는 권한이 있는지 여부를 확인 하는 데 사용 되는 [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) 개체의 목록입니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-613">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="7bddb-614">32KB</span><span class="sxs-lookup"><span data-stu-id="7bddb-614">32 KB</span></span> | <span data-ttu-id="7bddb-615">앱에서 전송하는 서버 버퍼의 최대 바이트 수입니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-615">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="7bddb-616">이 값을 늘리면 서버가 더 큰 메시지를 전송할 수 있지만 메모리 소비에 부정적인 영향을 줄 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-616">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="7bddb-617">모든 전송을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-617">All Transports are enabled.</span></span> | <span data-ttu-id="7bddb-618">클라이언트에서 연결 하는 데 사용할 수 있는 전송을 제한할 수 있는 `HttpTransportType` 값의 비트 플래그 열거형입니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-618">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="7bddb-619">아래를 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="7bddb-619">See below.</span></span> | <span data-ttu-id="7bddb-620">롱 폴링 전송과 관련된 추가 옵션입니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-620">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="7bddb-621">아래를 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="7bddb-621">See below.</span></span> | <span data-ttu-id="7bddb-622">WebSockets 전송과 관련된 추가 옵션입니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-622">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="7bddb-623">긴 폴링 전송에는 `LongPolling` 속성을 사용 하 여 구성할 수 있는 추가 옵션이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-623">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="7bddb-624">옵션</span><span class="sxs-lookup"><span data-stu-id="7bddb-624">Option</span></span> | <span data-ttu-id="7bddb-625">기본값</span><span class="sxs-lookup"><span data-stu-id="7bddb-625">Default Value</span></span> | <span data-ttu-id="7bddb-626">Description</span><span class="sxs-lookup"><span data-stu-id="7bddb-626">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="7bddb-627">90초</span><span class="sxs-lookup"><span data-stu-id="7bddb-627">90 seconds</span></span> | <span data-ttu-id="7bddb-628">단일 폴링 요청을 종료하기 전에 서버가 메시지를 클라이언트에 전송할 때까지 대기하는 최대 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-628">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="7bddb-629">이 값을 줄이면 클라이언트는 더 자주 새 폴링 요청을 발행합니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-629">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="7bddb-630">WebSocket 전송은 `WebSockets` 속성을 사용 하 여 구성할 수 있는 추가 옵션을 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-630">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="7bddb-631">옵션</span><span class="sxs-lookup"><span data-stu-id="7bddb-631">Option</span></span> | <span data-ttu-id="7bddb-632">기본값</span><span class="sxs-lookup"><span data-stu-id="7bddb-632">Default Value</span></span> | <span data-ttu-id="7bddb-633">Description</span><span class="sxs-lookup"><span data-stu-id="7bddb-633">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="7bddb-634">5초</span><span class="sxs-lookup"><span data-stu-id="7bddb-634">5 seconds</span></span> | <span data-ttu-id="7bddb-635">서버가 닫힌 후 클라이언트가 이 시간 제한 내에 닫기에 실패하면 연결이 종료됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-635">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="7bddb-636">`Sec-WebSocket-Protocol` 헤더를 사용자 지정 값으로 설정 하는 데 사용할 수 있는 대리자입니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-636">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="7bddb-637">이 대리자는 클라이언트에서 요청한 값을 입력으로 받아서 원하는 값을 반환해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-637">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="7bddb-638">클라이언트 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="7bddb-638">Configure client options</span></span>

<span data-ttu-id="7bddb-639">클라이언트 옵션은 `HubConnectionBuilder` 형식 (.NET 및 JavaScript 클라이언트에서 사용 가능)에서 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-639">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="7bddb-640">Java 클라이언트 에서도 사용할 수 있지만 `HttpHubConnectionBuilder` 하위 클래스는 작성기 구성 옵션과 `HubConnection` 자체를 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-640">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="7bddb-641">로깅 구성</span><span class="sxs-lookup"><span data-stu-id="7bddb-641">Configure logging</span></span>

<span data-ttu-id="7bddb-642">로깅은 `ConfigureLogging` 메서드를 사용 하 여 .NET 클라이언트에서 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-642">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="7bddb-643">로깅 공급자 및 필터는 서버와 동일한 방법으로 등록할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-643">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="7bddb-644">자세한 내용은 [ASP.NET Core의 로깅](xref:fundamentals/logging/index) 문서를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="7bddb-644">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="7bddb-645">로깅 공급자를 등록하는 데 필요한 패키지를 설치해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-645">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="7bddb-646">전체 목록은 문서에서 [기본 제공 로깅 공급자](xref:fundamentals/logging/index#built-in-logging-providers) 섹션을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="7bddb-646">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="7bddb-647">예를 들어 콘솔 로깅을 사용 하도록 설정 하려면 `Microsoft.Extensions.Logging.Console` NuGet 패키지를 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-647">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="7bddb-648">`AddConsole` 확장 메서드를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-648">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="7bddb-649">JavaScript 클라이언트에서 유사한 `configureLogging` 메서드가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-649">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="7bddb-650">생성할 로그 메시지의 최소 수준을 나타내는 `LogLevel` 값을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-650">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="7bddb-651">로그는 브라우저 콘솔 창에 기록됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-651">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="7bddb-652">로깅을 완전히 사용 하지 않도록 설정 하려면 `configureLogging` 메서드에서 `signalR.LogLevel.None`를 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-652">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="7bddb-653">로깅에 대 한 자세한 내용은 [SignalR 진단 설명서](xref:signalr/diagnostics)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="7bddb-653">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="7bddb-654">SignalR Java 클라이언트는 [SLF4J](https://www.slf4j.org/) 라이브러리를 사용 하 여 로깅을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-654">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="7bddb-655">이는 라이브러리의 사용자가 특정 로깅 종속성을 가져와 자신의 특정 로깅 구현을 선택할 수 있게 해 주는 상위 수준 로깅 API입니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-655">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="7bddb-656">다음 코드 조각에서는 SignalR Java 클라이언트와 함께 `java.util.logging`를 사용 하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-656">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="7bddb-657">종속성에서 로깅을 구성 하지 않으면 SLF4J는 다음과 같은 경고 메시지와 함께 기본 작업 없음로 거를 로드 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-657">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="7bddb-658">이는 무시 해도 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-658">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="7bddb-659">허용되는 전송 구성</span><span class="sxs-lookup"><span data-stu-id="7bddb-659">Configure allowed transports</span></span>

<span data-ttu-id="7bddb-660">SignalR에서 사용 하는 전송은 `WithUrl` 호출 (JavaScript의`withUrl`)에서 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-660">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="7bddb-661">`HttpTransportType` 값의 비트 or를 사용 하 여 지정 된 전송만 사용 하도록 클라이언트를 제한할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-661">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="7bddb-662">기본적으로 모든 전송은 활성화되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-662">All transports are enabled by default.</span></span>

<span data-ttu-id="7bddb-663">예를 들어 다음 코드는 서버-전송 이벤트 전송을 비활성화시키고 WebSocket 및 롱 폴링 연결만 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-663">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="7bddb-664">JavaScript 클라이언트에서 `withUrl`에 제공 된 options 개체의 `transport` 필드를 설정 하 여 전송이 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-664">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

### <a name="configure-bearer-authentication"></a><span data-ttu-id="7bddb-665">전달자 인증 구성</span><span class="sxs-lookup"><span data-stu-id="7bddb-665">Configure bearer authentication</span></span>

<span data-ttu-id="7bddb-666">SignalR 요청과 함께 인증 데이터를 제공 하려면 `AccessTokenProvider` 옵션 (JavaScript에서`accessTokenFactory`)을 사용 하 여 원하는 액세스 토큰을 반환 하는 함수를 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-666">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="7bddb-667">.NET 클라이언트에서이 액세스 토큰은 HTTP "전달자 인증" 토큰으로 전달 됩니다 (`Bearer`형식으로 `Authorization` 헤더 사용).</span><span class="sxs-lookup"><span data-stu-id="7bddb-667">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="7bddb-668">JavaScript 클라이언트에서 액세스 토큰은 브라우저 Api가 헤더를 적용 하는 기능을 제한 하는 경우를 **제외** 하 고 전달자 토큰으로 사용 됩니다 (특히 서버에서 보낸 이벤트와 websocket 요청에서).</span><span class="sxs-lookup"><span data-stu-id="7bddb-668">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="7bddb-669">이러한 경우 액세스 토큰은 `access_token`쿼리 문자열 값으로 제공 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-669">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="7bddb-670">.NET 클라이언트에서 `AccessTokenProvider` 옵션은 `WithUrl`의 옵션 대리자를 사용 하 여 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-670">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="7bddb-671">JavaScript 클라이언트에서 액세스 토큰은 `withUrl`의 options 개체에 `accessTokenFactory` 필드를 설정 하 여 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-671">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="7bddb-672">SignalR Java 클라이언트에서 [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)에 액세스 토큰 팩터리를 제공 하 여 인증에 사용할 전달자 토큰을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-672">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="7bddb-673">[WithAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) 를 사용 하 여 [rxjava](https://github.com/ReactiveX/RxJava) [Single\<문자열 >](https://reactivex.io/documentation/single.html)를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-673">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="7bddb-674">[단일. 지연](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)에 대 한 호출을 사용 하 여 클라이언트에 대 한 액세스 토큰을 생성 하는 논리를 작성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-674">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="7bddb-675">시간 제한 및 연결 유지 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="7bddb-675">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="7bddb-676">제한 시간 및 연결 유지 동작을 구성 하기 위한 추가 옵션은 `HubConnection` 개체 자체에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-676">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="7bddb-677">.NET</span><span class="sxs-lookup"><span data-stu-id="7bddb-677">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="7bddb-678">옵션</span><span class="sxs-lookup"><span data-stu-id="7bddb-678">Option</span></span> | <span data-ttu-id="7bddb-679">기본값</span><span class="sxs-lookup"><span data-stu-id="7bddb-679">Default value</span></span> | <span data-ttu-id="7bddb-680">Description</span><span class="sxs-lookup"><span data-stu-id="7bddb-680">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="7bddb-681">30초(30000밀리초)</span><span class="sxs-lookup"><span data-stu-id="7bddb-681">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="7bddb-682">서버 활동 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-682">Timeout for server activity.</span></span> <span data-ttu-id="7bddb-683">서버가이 간격 내에 메시지를 보내지 않은 경우 클라이언트는 서버 연결을 끊고 `Closed` 이벤트 (JavaScript의`onclose`)를 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-683">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="7bddb-684">이 값은 ping 메시지 **를 서버에서 보내고 시간** 제한 간격 내에 클라이언트에서 받을 수 있도록 충분히 커야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-684">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="7bddb-685">권장 값은 ping이 도착할 때까지 시간을 허용 하기 위해 서버의 `KeepAliveInterval` 값을 두 번 이상 사용 하는 숫자입니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-685">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="7bddb-686">15초</span><span class="sxs-lookup"><span data-stu-id="7bddb-686">15 seconds</span></span> | <span data-ttu-id="7bddb-687">초기 서버 핸드셰이크에 대한 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-687">Timeout for initial server handshake.</span></span> <span data-ttu-id="7bddb-688">서버에서이 간격으로 핸드셰이크 응답을 보내지 않는 경우 클라이언트는 핸드셰이크를 취소 하 고 `Closed` 이벤트 (JavaScript의`onclose`)를 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-688">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="7bddb-689">이 설정은 심각한 네트워크 지연으로 인해 핸드셰이크 시간 제한 오류가 발생하는 경우에만 수정해야 하는 고급 설정입니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-689">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="7bddb-690">핸드셰이크 프로세스에 대 한 자세한 내용은 [SignalR Hub 프로토콜 사양](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="7bddb-690">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

<span data-ttu-id="7bddb-691">.NET 클라이언트에서 시간 제한 값은 `TimeSpan` 값으로 지정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-691">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="7bddb-692">JavaScript</span><span class="sxs-lookup"><span data-stu-id="7bddb-692">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="7bddb-693">옵션</span><span class="sxs-lookup"><span data-stu-id="7bddb-693">Option</span></span> | <span data-ttu-id="7bddb-694">기본값</span><span class="sxs-lookup"><span data-stu-id="7bddb-694">Default value</span></span> | <span data-ttu-id="7bddb-695">Description</span><span class="sxs-lookup"><span data-stu-id="7bddb-695">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="7bddb-696">30초(30000밀리초)</span><span class="sxs-lookup"><span data-stu-id="7bddb-696">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="7bddb-697">서버 활동 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-697">Timeout for server activity.</span></span> <span data-ttu-id="7bddb-698">서버가이 간격 내에 메시지를 보내지 않은 경우 클라이언트는 서버 연결을 끊고 `onclose` 이벤트를 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-698">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="7bddb-699">이 값은 ping 메시지 **를 서버에서 보내고 시간** 제한 간격 내에 클라이언트에서 받을 수 있도록 충분히 커야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-699">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="7bddb-700">권장 값은 ping이 도착할 때까지 시간을 허용 하기 위해 서버의 `KeepAliveInterval` 값을 두 번 이상 사용 하는 숫자입니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-700">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |

# <a name="java"></a>[<span data-ttu-id="7bddb-701">Java</span><span class="sxs-lookup"><span data-stu-id="7bddb-701">Java</span></span>](#tab/java)

| <span data-ttu-id="7bddb-702">옵션</span><span class="sxs-lookup"><span data-stu-id="7bddb-702">Option</span></span> | <span data-ttu-id="7bddb-703">기본값</span><span class="sxs-lookup"><span data-stu-id="7bddb-703">Default value</span></span> | <span data-ttu-id="7bddb-704">Description</span><span class="sxs-lookup"><span data-stu-id="7bddb-704">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="7bddb-705">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="7bddb-705">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="7bddb-706">30초(30000밀리초)</span><span class="sxs-lookup"><span data-stu-id="7bddb-706">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="7bddb-707">서버 활동 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-707">Timeout for server activity.</span></span> <span data-ttu-id="7bddb-708">서버가이 간격 내에 메시지를 보내지 않은 경우 클라이언트는 서버 연결을 끊고 `onClose` 이벤트를 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-708">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="7bddb-709">이 값은 ping 메시지 **를 서버에서 보내고 시간** 제한 간격 내에 클라이언트에서 받을 수 있도록 충분히 커야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-709">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="7bddb-710">권장 값은 ping이 도착할 시간을 허용 하기 위해 서버의 `KeepAliveInterval` 값을 두 번 이상 사용 하는 숫자입니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-710">The recommended value is a number at least double the server's `KeepAliveInterval` value, to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="7bddb-711">15초</span><span class="sxs-lookup"><span data-stu-id="7bddb-711">15 seconds</span></span> | <span data-ttu-id="7bddb-712">초기 서버 핸드셰이크에 대한 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-712">Timeout for initial server handshake.</span></span> <span data-ttu-id="7bddb-713">서버가이 간격 내에 핸드셰이크 응답을 보내지 않는 경우 클라이언트는 핸드셰이크를 취소 하 고 `onClose` 이벤트를 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-713">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="7bddb-714">이 설정은 심각한 네트워크 지연으로 인해 핸드셰이크 시간 제한 오류가 발생하는 경우에만 수정해야 하는 고급 설정입니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-714">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="7bddb-715">핸드셰이크 프로세스에 대 한 자세한 내용은 [SignalR Hub 프로토콜 사양](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="7bddb-715">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="7bddb-716">추가 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="7bddb-716">Configure additional options</span></span>

<span data-ttu-id="7bddb-717">추가 옵션은 Java 클라이언트의 `HttpHubConnectionBuilder`에서 `HubConnectionBuilder` 또는 다양 한 구성 Api에 대 한 `WithUrl` (JavaScript의`withUrl`) 방법으로 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-717">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="7bddb-718">.NET</span><span class="sxs-lookup"><span data-stu-id="7bddb-718">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="7bddb-719">.NET 옵션</span><span class="sxs-lookup"><span data-stu-id="7bddb-719">.NET Option</span></span> |  <span data-ttu-id="7bddb-720">기본값</span><span class="sxs-lookup"><span data-stu-id="7bddb-720">Default value</span></span> | <span data-ttu-id="7bddb-721">Description</span><span class="sxs-lookup"><span data-stu-id="7bddb-721">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="7bddb-722">HTTP 요청에서 전달자 인증 토큰으로 제공된 문자열을 반환하는 함수입니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-722">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="7bddb-723">협상 단계를 건너뛰려면 `true`로 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-723">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="7bddb-724">**Websocket 전송이 유일 하 게 설정 된 전송 인 경우에만 지원 됩니다**.</span><span class="sxs-lookup"><span data-stu-id="7bddb-724">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="7bddb-725">Azure SignalR Service를 사용하는 경우에는 이 설정을 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-725">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="7bddb-726">Empty</span><span class="sxs-lookup"><span data-stu-id="7bddb-726">Empty</span></span> | <span data-ttu-id="7bddb-727">요청을 인증하기 위해 전송할 TLS 인증서의 컬렉션입니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-727">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="7bddb-728">Empty</span><span class="sxs-lookup"><span data-stu-id="7bddb-728">Empty</span></span> | <span data-ttu-id="7bddb-729">모든 HTTP 요청과 함께 전송할 HTTP 쿠키의 컬렉션입니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-729">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="7bddb-730">Empty</span><span class="sxs-lookup"><span data-stu-id="7bddb-730">Empty</span></span> | <span data-ttu-id="7bddb-731">모든 HTTP 요청과 함께 전송할 자격 증명입니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-731">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="7bddb-732">5초</span><span class="sxs-lookup"><span data-stu-id="7bddb-732">5 seconds</span></span> | <span data-ttu-id="7bddb-733">Websocket에만 해당됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-733">WebSockets only.</span></span> <span data-ttu-id="7bddb-734">클라이언트가 서버를 닫은 후 닫기 요청을 확인하기 위해 대기하는 최대 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-734">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="7bddb-735">서버가 이 시간 내에 종료를 승인하지 않으면 클라이언트가 연결을 끊습니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-735">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="7bddb-736">Empty</span><span class="sxs-lookup"><span data-stu-id="7bddb-736">Empty</span></span> | <span data-ttu-id="7bddb-737">모든 HTTP 요청과 함께 보낼 추가 HTTP 헤더의 맵입니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-737">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="7bddb-738">HTTP 요청을 보내는 데 사용 되는 `HttpMessageHandler`를 구성 하거나 대체 하는 데 사용할 수 있는 대리자입니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-738">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="7bddb-739">WebSocket 연결에는 사용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-739">Not used for WebSocket connections.</span></span> <span data-ttu-id="7bddb-740">이 대리자는 null이 아닌 값을 반환해야 하며 매개 변수로 기본값을 받습니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-740">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="7bddb-741">해당 기본값에 대 한 설정을 수정 하 고 반환 하거나 새 `HttpMessageHandler` 인스턴스를 반환 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-741">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="7bddb-742">**처리기를 대체할 때 제공 된 처리기에서 유지할 설정을 복사 해야 합니다. 그렇지 않으면 구성 된 옵션 (예: 쿠키 및 헤더)이 새 처리기에 적용 되지 않습니다.**</span><span class="sxs-lookup"><span data-stu-id="7bddb-742">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="7bddb-743">HTTP 요청을 전송할 때 사용할 HTTP 프록시입니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-743">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="7bddb-744">HTTP 및 WebSockets 요청에 대한 기본 자격 증명을 전송하려면 이 부울 값을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-744">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="7bddb-745">그러면 Windows 인증을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-745">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="7bddb-746">추가적인 WebSocket 옵션을 구성할 수 있는 대리자입니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-746">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="7bddb-747">옵션을 구성 하는 데 사용할 수 있는 [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) 의 인스턴스를 수신 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-747">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="7bddb-748">JavaScript</span><span class="sxs-lookup"><span data-stu-id="7bddb-748">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="7bddb-749">JavaScript 옵션</span><span class="sxs-lookup"><span data-stu-id="7bddb-749">JavaScript Option</span></span> | <span data-ttu-id="7bddb-750">기본값</span><span class="sxs-lookup"><span data-stu-id="7bddb-750">Default Value</span></span> | <span data-ttu-id="7bddb-751">Description</span><span class="sxs-lookup"><span data-stu-id="7bddb-751">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="7bddb-752">HTTP 요청에서 전달자 인증 토큰으로 제공된 문자열을 반환하는 함수입니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-752">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="7bddb-753">협상 단계를 건너뛰려면 `true`로 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-753">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="7bddb-754">**Websocket 전송이 유일 하 게 설정 된 전송 인 경우에만 지원 됩니다**.</span><span class="sxs-lookup"><span data-stu-id="7bddb-754">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="7bddb-755">Azure SignalR Service를 사용하는 경우에는 이 설정을 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-755">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="7bddb-756">Java</span><span class="sxs-lookup"><span data-stu-id="7bddb-756">Java</span></span>](#tab/java)

| <span data-ttu-id="7bddb-757">Java 옵션</span><span class="sxs-lookup"><span data-stu-id="7bddb-757">Java Option</span></span> | <span data-ttu-id="7bddb-758">기본값</span><span class="sxs-lookup"><span data-stu-id="7bddb-758">Default Value</span></span> | <span data-ttu-id="7bddb-759">Description</span><span class="sxs-lookup"><span data-stu-id="7bddb-759">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="7bddb-760">HTTP 요청에서 전달자 인증 토큰으로 제공된 문자열을 반환하는 함수입니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-760">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="7bddb-761">협상 단계를 건너뛰려면 `true`로 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-761">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="7bddb-762">**Websocket 전송이 유일 하 게 설정 된 전송 인 경우에만 지원 됩니다**.</span><span class="sxs-lookup"><span data-stu-id="7bddb-762">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="7bddb-763">Azure SignalR Service를 사용하는 경우에는 이 설정을 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-763">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="7bddb-764">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="7bddb-764">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="7bddb-765">Empty</span><span class="sxs-lookup"><span data-stu-id="7bddb-765">Empty</span></span> | <span data-ttu-id="7bddb-766">모든 HTTP 요청과 함께 보낼 추가 HTTP 헤더의 맵입니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-766">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="7bddb-767">.NET 클라이언트에서 이러한 옵션은 `WithUrl`에 제공 된 옵션 대리자로 수정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-767">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="7bddb-768">JavaScript 클라이언트에서 이러한 옵션은 `withUrl`에 제공 된 JavaScript 개체에 제공 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bddb-768">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="7bddb-769">Java 클라이언트에서 이러한 옵션은에서 반환 되는 `HttpHubConnectionBuilder`에 대 한 메서드로 구성할 수 있습니다 `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="7bddb-769">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="7bddb-770">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="7bddb-770">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end