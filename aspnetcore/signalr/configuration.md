---
title: ASP.NET Core SignalR 구성
author: bradygaster
description: ASP.NET Core SignalR 앱을 구성하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 08/05/2019
uid: signalr/configuration
ms.openlocfilehash: 66f274fcda27392091de6b4be8c7221bc87b7585
ms.sourcegitcommit: c452e6af92e130413106c4863193f377cde4cd9c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/10/2019
ms.locfileid: "72246486"
---
# <a name="aspnet-core-signalr-configuration"></a><span data-ttu-id="11e2a-103">ASP.NET Core SignalR 구성</span><span class="sxs-lookup"><span data-stu-id="11e2a-103">ASP.NET Core SignalR configuration</span></span>

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="11e2a-104">JSON/MessagePack serialization 옵션</span><span class="sxs-lookup"><span data-stu-id="11e2a-104">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="11e2a-105">ASP.NET Core SignalR는 메시지 인코딩에 대 한 두 가지 프로토콜을 지원 합니다. [JSON](https://www.json.org/) 및 [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="11e2a-105">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="11e2a-106">각 프로토콜에는 serialization 구성 옵션이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-106">Each protocol has serialization configuration options.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="11e2a-107">JSON serialization은 [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) 확장 메서드를 사용 하 여 서버에서 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-107">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="11e2a-108">`Startup.ConfigureServices`의 [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) 뒤 @no__t에-0을 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-108">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="11e2a-109">`AddJsonProtocol` 메서드는 `options` 개체를 전달받는 대리자를 받습니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-109">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="11e2a-110">해당 개체의 [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) 속성은 인수 및 반환 값의 serialization을 구성 하는 데 사용할 수 있는 @no__t 1 <xref:System.Text.Json.JsonSerializerOptions> 개체입니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-110">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="11e2a-111">자세한 내용은 [system.object 설명서](/dotnet/api/system.text.json)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="11e2a-111">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="11e2a-112">예를 들어 기본 "camelCase" 이름 대신 속성 이름의 대/소문자를 변경 하지 않도록 serializer를 구성 하려면 `Startup.ConfigureServices`에서 다음 코드를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-112">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="11e2a-113">.NET 클라이언트에서는 [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)에 동일한 `AddJsonProtocol` 확장 메서드가 존재합니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-113">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="11e2a-114">이 확장 메서드를 확인하려면 `Microsoft.Extensions.DependencyInjection` 네임스페이스를 임포트해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-114">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="11e2a-115">Newtonsoft.json로 전환 합니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-115">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="11e2a-116">@No__t-1에서 지원 되지 않는 `Newtonsoft.Json`의 기능이 필요한 경우 [newtonsoft.json으로 전환](xref:migration/22-to-30#switch-to-newtonsoftjson)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="11e2a-116">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="11e2a-117">JSON serialization을 사용 하 여 서버에 구성할 수 있습니다 합니다 [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) 후 추가할 수 있는 확장 메서드 [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) 에서 프로그램 `Startup.ConfigureServices` 메서드.</span><span class="sxs-lookup"><span data-stu-id="11e2a-117">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="11e2a-118">`AddJsonProtocol` 메서드는 `options` 개체를 전달받는 대리자를 받습니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-118">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="11e2a-119">이 개체의 [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) 속성은 JSON.NET의 `JsonSerializerSettings` 개체로, 인수 및 반환 값의 직렬화를 구성하는 데 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-119">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="11e2a-120">자세한 내용은 [JSON.NET 설명서](https://www.newtonsoft.com/json/help/html/Introduction.htm)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="11e2a-120">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="11e2a-121">예를 들어 기본 "camelCase" 이름 대신 "작업 기 사례" 속성 이름을 사용 하도록 serializer를 구성 하려면 `Startup.ConfigureServices`에서 다음 코드를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-121">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="11e2a-122">.NET 클라이언트에서는 [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)에 동일한 `AddJsonProtocol` 확장 메서드가 존재합니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-122">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="11e2a-123">이 확장 메서드를 확인하려면 `Microsoft.Extensions.DependencyInjection` 네임스페이스를 임포트해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-123">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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

::: moniker-end

> [!NOTE]
> <span data-ttu-id="11e2a-124">현재 JavaScript 클라이언트에서는 JSON 직렬화를 구성할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-124">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="11e2a-125">MessagePack serialization 옵션</span><span class="sxs-lookup"><span data-stu-id="11e2a-125">MessagePack serialization options</span></span>

<span data-ttu-id="11e2a-126">MessagePack 직렬화는 [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) 호출에 대리자를 제공하여 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-126">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="11e2a-127">더 자세한 내용은 [SignalR의 MessagePack](xref:signalr/messagepackhubprotocol)을 참고하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-127">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="11e2a-128">현재 JavaScript 클라이언트에서는 MessagePack 직렬화를 구성할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-128">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="11e2a-129">서버 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="11e2a-129">Configure server options</span></span>

<span data-ttu-id="11e2a-130">다음 표는 SignalR 허브를 구성하기 위한 옵션을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-130">The following table describes options for configuring SignalR hubs:</span></span>

::: moniker range=">= aspnetcore-3.0"

| <span data-ttu-id="11e2a-131">옵션</span><span class="sxs-lookup"><span data-stu-id="11e2a-131">Option</span></span> | <span data-ttu-id="11e2a-132">Default Value</span><span class="sxs-lookup"><span data-stu-id="11e2a-132">Default Value</span></span> | <span data-ttu-id="11e2a-133">설명</span><span class="sxs-lookup"><span data-stu-id="11e2a-133">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="11e2a-134">30초</span><span class="sxs-lookup"><span data-stu-id="11e2a-134">30 seconds</span></span> | <span data-ttu-id="11e2a-135">서버는이 간격 내에 메시지를 받지 못한 경우 (keep-alive 포함) 클라이언트의 연결을 해제 하는 것으로 간주 합니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-135">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="11e2a-136">이를 구현 하는 방법으로 인해 클라이언트에서 실제로 연결이 끊어진 것으로 표시 되는 시간 제한 간격 보다 오래 걸릴 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-136">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="11e2a-137">권장 값은 `KeepAliveInterval` 값의 두 배가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-137">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="11e2a-138">15초</span><span class="sxs-lookup"><span data-stu-id="11e2a-138">15 seconds</span></span> | <span data-ttu-id="11e2a-139">클라이언트가 이 시간 제한 내에 초기 핸드셰이크 메시지를 전송하지 않으면 연결이 닫힙니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-139">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="11e2a-140">이 설정은 심각한 네트워크 지연으로 인해 핸드셰이크 시간 제한 오류가 발생하는 경우에만 수정해야 하는 고급 설정입니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-140">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="11e2a-141">핸드셰이크 프로세스에 대한 보다 자세한 내용은 [SignalR 허브 프로토콜 사양](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)을 참고하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-141">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="11e2a-142">15초</span><span class="sxs-lookup"><span data-stu-id="11e2a-142">15 seconds</span></span> | <span data-ttu-id="11e2a-143">서버가 이 간격 내에 메시지를 전송하지 않으면 자동으로 ping 메시지가 전송되어 연결이 열린 상태로 유지됩니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-143">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="11e2a-144">`KeepAliveInterval`을 변경할 경우 클라이언트의 `ServerTimeout`/`serverTimeoutInMilliseconds` 설정도 변경하십시오.</span><span class="sxs-lookup"><span data-stu-id="11e2a-144">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="11e2a-145">권장되는 `ServerTimeout`/`serverTimeoutInMilliseconds` 값은 `KeepAliveInterval` 값의 두 배입니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-145">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="11e2a-146">설치된 모든 프로토콜</span><span class="sxs-lookup"><span data-stu-id="11e2a-146">All installed protocols</span></span> | <span data-ttu-id="11e2a-147">허브가 지원하는 프로토콜입니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-147">Protocols supported by this hub.</span></span> <span data-ttu-id="11e2a-148">기본적으로 서버에 등록된 모든 프로토콜이 허용되지만 이 목록에서 프로토콜을 제거하여 개별 허브에 대해 특정 프로토콜을 비활성화시킬 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-148">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="11e2a-149">이 값이 `true`면 Hub 메서드에서 예외가 발생할 경우 자세한 예외 메시지가 클라이언트로 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-149">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="11e2a-150">예외 메시지에는 민감한 정보가 포함되어 있을 수 있으므로 기본값은 `false`입니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-150">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="11e2a-151">클라이언트 업로드 스트림에 대해 버퍼링 할 수 있는 최대 항목 수입니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-151">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="11e2a-152">이 제한에 도달 하면 서버에서 스트림 항목을 처리할 때까지 호출 처리가 차단 됩니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-152">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="11e2a-153">32 KB</span><span class="sxs-lookup"><span data-stu-id="11e2a-153">32 KB</span></span> | <span data-ttu-id="11e2a-154">들어오는 단일 허브 메시지의 최대 크기입니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-154">Maximum size of a single incoming hub message.</span></span> |

::: moniker-end

::: moniker range="= aspnetcore-2.2"

| <span data-ttu-id="11e2a-155">옵션</span><span class="sxs-lookup"><span data-stu-id="11e2a-155">Option</span></span> | <span data-ttu-id="11e2a-156">Default Value</span><span class="sxs-lookup"><span data-stu-id="11e2a-156">Default Value</span></span> | <span data-ttu-id="11e2a-157">설명</span><span class="sxs-lookup"><span data-stu-id="11e2a-157">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="11e2a-158">30초</span><span class="sxs-lookup"><span data-stu-id="11e2a-158">30 seconds</span></span> | <span data-ttu-id="11e2a-159">서버는이 간격 내에 메시지를 받지 못한 경우 (keep-alive 포함) 클라이언트의 연결을 해제 하는 것으로 간주 합니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-159">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="11e2a-160">이를 구현 하는 방법으로 인해 클라이언트에서 실제로 연결이 끊어진 것으로 표시 되는 시간 제한 간격 보다 오래 걸릴 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-160">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="11e2a-161">권장 값은 `KeepAliveInterval` 값의 두 배가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-161">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="11e2a-162">15초</span><span class="sxs-lookup"><span data-stu-id="11e2a-162">15 seconds</span></span> | <span data-ttu-id="11e2a-163">클라이언트가 이 시간 제한 내에 초기 핸드셰이크 메시지를 전송하지 않으면 연결이 닫힙니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-163">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="11e2a-164">이 설정은 심각한 네트워크 지연으로 인해 핸드셰이크 시간 제한 오류가 발생하는 경우에만 수정해야 하는 고급 설정입니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-164">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="11e2a-165">핸드셰이크 프로세스에 대한 보다 자세한 내용은 [SignalR 허브 프로토콜 사양](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)을 참고하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-165">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="11e2a-166">15초</span><span class="sxs-lookup"><span data-stu-id="11e2a-166">15 seconds</span></span> | <span data-ttu-id="11e2a-167">서버가 이 간격 내에 메시지를 전송하지 않으면 자동으로 ping 메시지가 전송되어 연결이 열린 상태로 유지됩니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-167">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="11e2a-168">`KeepAliveInterval`을 변경할 경우 클라이언트의 `ServerTimeout`/`serverTimeoutInMilliseconds` 설정도 변경하십시오.</span><span class="sxs-lookup"><span data-stu-id="11e2a-168">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="11e2a-169">권장되는 `ServerTimeout`/`serverTimeoutInMilliseconds` 값은 `KeepAliveInterval` 값의 두 배입니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-169">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="11e2a-170">설치된 모든 프로토콜</span><span class="sxs-lookup"><span data-stu-id="11e2a-170">All installed protocols</span></span> | <span data-ttu-id="11e2a-171">허브가 지원하는 프로토콜입니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-171">Protocols supported by this hub.</span></span> <span data-ttu-id="11e2a-172">기본적으로 서버에 등록된 모든 프로토콜이 허용되지만 이 목록에서 프로토콜을 제거하여 개별 허브에 대해 특정 프로토콜을 비활성화시킬 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-172">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="11e2a-173">이 값이 `true`면 Hub 메서드에서 예외가 발생할 경우 자세한 예외 메시지가 클라이언트로 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-173">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="11e2a-174">예외 메시지에는 민감한 정보가 포함되어 있을 수 있으므로 기본값은 `false`입니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-174">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

::: moniker-end

::: moniker range="= aspnetcore-2.1"

| <span data-ttu-id="11e2a-175">옵션</span><span class="sxs-lookup"><span data-stu-id="11e2a-175">Option</span></span> | <span data-ttu-id="11e2a-176">Default Value</span><span class="sxs-lookup"><span data-stu-id="11e2a-176">Default Value</span></span> | <span data-ttu-id="11e2a-177">설명</span><span class="sxs-lookup"><span data-stu-id="11e2a-177">Description</span></span> |
| ------ | ------------- | ----------- |
| `HandshakeTimeout` | <span data-ttu-id="11e2a-178">15초</span><span class="sxs-lookup"><span data-stu-id="11e2a-178">15 seconds</span></span> | <span data-ttu-id="11e2a-179">클라이언트가 이 시간 제한 내에 초기 핸드셰이크 메시지를 전송하지 않으면 연결이 닫힙니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-179">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="11e2a-180">이 설정은 심각한 네트워크 지연으로 인해 핸드셰이크 시간 제한 오류가 발생하는 경우에만 수정해야 하는 고급 설정입니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-180">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="11e2a-181">핸드셰이크 프로세스에 대한 보다 자세한 내용은 [SignalR 허브 프로토콜 사양](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)을 참고하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-181">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="11e2a-182">15초</span><span class="sxs-lookup"><span data-stu-id="11e2a-182">15 seconds</span></span> | <span data-ttu-id="11e2a-183">서버가 이 간격 내에 메시지를 전송하지 않으면 자동으로 ping 메시지가 전송되어 연결이 열린 상태로 유지됩니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-183">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="11e2a-184">`KeepAliveInterval`을 변경할 경우 클라이언트의 `ServerTimeout`/`serverTimeoutInMilliseconds` 설정도 변경하십시오.</span><span class="sxs-lookup"><span data-stu-id="11e2a-184">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="11e2a-185">권장되는 `ServerTimeout`/`serverTimeoutInMilliseconds` 값은 `KeepAliveInterval` 값의 두 배입니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-185">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="11e2a-186">설치된 모든 프로토콜</span><span class="sxs-lookup"><span data-stu-id="11e2a-186">All installed protocols</span></span> | <span data-ttu-id="11e2a-187">허브가 지원하는 프로토콜입니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-187">Protocols supported by this hub.</span></span> <span data-ttu-id="11e2a-188">기본적으로 서버에 등록된 모든 프로토콜이 허용되지만 이 목록에서 프로토콜을 제거하여 개별 허브에 대해 특정 프로토콜을 비활성화시킬 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-188">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="11e2a-189">이 값이 `true`면 Hub 메서드에서 예외가 발생할 경우 자세한 예외 메시지가 클라이언트로 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-189">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="11e2a-190">예외 메시지에는 민감한 정보가 포함되어 있을 수 있으므로 기본값은 `false`입니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-190">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

::: moniker-end

<span data-ttu-id="11e2a-191">`Startup.ConfigureServices`에서 `AddSignalR` 호출에 옵션 대리자를 제공하여 모든 허브에 대한 옵션을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-191">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="11e2a-192">단일 허브에 대 한 옵션은 `AddSignalR`에 제공 된 전역 옵션을 재정의 하며, <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>을 사용 하 여 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-192">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="11e2a-193">고급 HTTP 구성 옵션</span><span class="sxs-lookup"><span data-stu-id="11e2a-193">Advanced HTTP configuration options</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="11e2a-194">전송 및 메모리 버퍼 관리와 관련된 고급 설정을 구성하려면 `HttpConnectionDispatcherOptions`를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-194">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="11e2a-195">이러한 옵션은 `Startup.Configure`의 [Maphub @ no__t-1T >](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) 에 대리자를 전달 하 여 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-195">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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
::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="11e2a-196">전송 및 메모리 버퍼 관리와 관련된 고급 설정을 구성하려면 `HttpConnectionDispatcherOptions`를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-196">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="11e2a-197">이러한 옵션은 `Startup.Configure`의 [Maphub @ no__t-1T >](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) 에 대리자를 전달 하 여 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-197">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

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

::: moniker-end

<span data-ttu-id="11e2a-198">다음 표에서는 ASP.NET Core SignalR의 고급 HTTP 옵션을 구성 하는 옵션을 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-198">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

::: moniker range=">= aspnetcore-3.0"

| <span data-ttu-id="11e2a-199">옵션</span><span class="sxs-lookup"><span data-stu-id="11e2a-199">Option</span></span> | <span data-ttu-id="11e2a-200">Default Value</span><span class="sxs-lookup"><span data-stu-id="11e2a-200">Default Value</span></span> | <span data-ttu-id="11e2a-201">설명</span><span class="sxs-lookup"><span data-stu-id="11e2a-201">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="11e2a-202">32 KB</span><span class="sxs-lookup"><span data-stu-id="11e2a-202">32 KB</span></span> | <span data-ttu-id="11e2a-203">클라이언트에서 수신 된 백 압력을 적용 하기 전에 버퍼링 되는 최대 바이트 수입니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-203">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="11e2a-204">이 값을 늘리면 서버는 백 압력을 적용 하지 않고 더 큰 메시지를 더 빨리 수신할 수 있지만 메모리 사용을 높일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-204">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="11e2a-205">허브 클래스에 적용된 `Authorize` 특성에서 자동으로 수집된 데이터입니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-205">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="11e2a-206">클라이언트가 허브에 연결할 권한이 있는지 확인하는 데 사용되는 [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) 개체의 목록입니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-206">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="11e2a-207">32 KB</span><span class="sxs-lookup"><span data-stu-id="11e2a-207">32 KB</span></span> | <span data-ttu-id="11e2a-208">서버에서 백 압력을 관찰 하기 전에 버퍼링 하는 앱에서 보낸 최대 바이트 수입니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-208">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="11e2a-209">이 값을 늘리면 서버에서 백 압력을 대기 하지 않고 더 큰 메시지를 더 빨리 버퍼링 할 수 있지만 메모리 소비가 증가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-209">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="11e2a-210">모든 전송을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-210">All Transports are enabled.</span></span> | <span data-ttu-id="11e2a-211">클라이언트에서 연결 하는 데 사용할 수 있는 전송을 제한할 수 있는 @no__t 값의 비트 플래그 열거형입니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-211">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="11e2a-212">아래를 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="11e2a-212">See below.</span></span> | <span data-ttu-id="11e2a-213">롱 폴링 전송과 관련된 추가 옵션입니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-213">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="11e2a-214">아래 내용을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="11e2a-214">See below.</span></span> | <span data-ttu-id="11e2a-215">WebSockets 전송과 관련된 추가 옵션입니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-215">Additional options specific to the WebSockets transport.</span></span> |

::: moniker-end

::: moniker range="< aspnetcore-3.0"

| <span data-ttu-id="11e2a-216">옵션</span><span class="sxs-lookup"><span data-stu-id="11e2a-216">Option</span></span> | <span data-ttu-id="11e2a-217">Default Value</span><span class="sxs-lookup"><span data-stu-id="11e2a-217">Default Value</span></span> | <span data-ttu-id="11e2a-218">설명</span><span class="sxs-lookup"><span data-stu-id="11e2a-218">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="11e2a-219">32 KB</span><span class="sxs-lookup"><span data-stu-id="11e2a-219">32 KB</span></span> | <span data-ttu-id="11e2a-220">클라이언트로부터 수신하는 서버 버퍼의 최대 바이트 수입니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-220">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="11e2a-221">이 값을 늘리면 서버가 더 큰 메시지를 수신할 수 있지만 메모리 소비에 부정적인 영향을 줄 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-221">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="11e2a-222">허브 클래스에 적용된 `Authorize` 특성에서 자동으로 수집된 데이터입니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-222">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="11e2a-223">클라이언트가 허브에 연결할 권한이 있는지 확인하는 데 사용되는 [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) 개체의 목록입니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-223">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="11e2a-224">32 KB</span><span class="sxs-lookup"><span data-stu-id="11e2a-224">32 KB</span></span> | <span data-ttu-id="11e2a-225">앱에서 전송하는 서버 버퍼의 최대 바이트 수입니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-225">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="11e2a-226">이 값을 늘리면 서버가 더 큰 메시지를 전송할 수 있지만 메모리 소비에 부정적인 영향을 줄 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-226">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="11e2a-227">모든 전송을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-227">All Transports are enabled.</span></span> | <span data-ttu-id="11e2a-228">클라이언트에서 연결 하는 데 사용할 수 있는 전송을 제한할 수 있는 @no__t 값의 비트 플래그 열거형입니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-228">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="11e2a-229">아래를 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="11e2a-229">See below.</span></span> | <span data-ttu-id="11e2a-230">롱 폴링 전송과 관련된 추가 옵션입니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-230">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="11e2a-231">아래 내용을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="11e2a-231">See below.</span></span> | <span data-ttu-id="11e2a-232">WebSockets 전송과 관련된 추가 옵션입니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-232">Additional options specific to the WebSockets transport.</span></span> |

::: moniker-end

<span data-ttu-id="11e2a-233">롱 폴링 전송에는 `LongPolling` 속성을 이용해서 구성할 수 있는 추가적인 옵션이 존재합니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-233">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="11e2a-234">옵션</span><span class="sxs-lookup"><span data-stu-id="11e2a-234">Option</span></span> | <span data-ttu-id="11e2a-235">Default Value</span><span class="sxs-lookup"><span data-stu-id="11e2a-235">Default Value</span></span> | <span data-ttu-id="11e2a-236">설명</span><span class="sxs-lookup"><span data-stu-id="11e2a-236">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="11e2a-237">90초</span><span class="sxs-lookup"><span data-stu-id="11e2a-237">90 seconds</span></span> | <span data-ttu-id="11e2a-238">단일 폴링 요청을 종료하기 전에 서버가 메시지를 클라이언트에 전송할 때까지 대기하는 최대 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-238">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="11e2a-239">이 값을 줄이면 클라이언트는 더 자주 새 폴링 요청을 발행합니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-239">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="11e2a-240">WebSocket 전송에는 `WebSockets` 속성을 이용해서 구성할 수 있는 추가적인 옵션이 존재합니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-240">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="11e2a-241">옵션</span><span class="sxs-lookup"><span data-stu-id="11e2a-241">Option</span></span> | <span data-ttu-id="11e2a-242">Default Value</span><span class="sxs-lookup"><span data-stu-id="11e2a-242">Default Value</span></span> | <span data-ttu-id="11e2a-243">설명</span><span class="sxs-lookup"><span data-stu-id="11e2a-243">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="11e2a-244">5초</span><span class="sxs-lookup"><span data-stu-id="11e2a-244">5 seconds</span></span> | <span data-ttu-id="11e2a-245">서버가 닫힌 후 클라이언트가 이 시간 제한 내에 닫기에 실패하면 연결이 종료됩니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-245">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="11e2a-246">사용자 지정 값으로 `Sec-WebSocket-Protocol` 헤더를 설정하기 위해 사용할 수 있는 대리자입니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-246">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="11e2a-247">이 대리자는 클라이언트에서 요청한 값을 입력으로 받아서 원하는 값을 반환해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-247">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="11e2a-248">클라이언트 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="11e2a-248">Configure client options</span></span>

<span data-ttu-id="11e2a-249">클라이언트 옵션은 `HubConnectionBuilder` 형식 (.NET 및 JavaScript 클라이언트에서 사용 가능)에서 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-249">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="11e2a-250">Java 클라이언트 에서도 사용할 수 있지만 `HttpHubConnectionBuilder` 서브 클래스는 `HubConnection` 자체 뿐만 아니라 작성기 구성 옵션을 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-250">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="11e2a-251">로깅 구성</span><span class="sxs-lookup"><span data-stu-id="11e2a-251">Configure logging</span></span>

<span data-ttu-id="11e2a-252">.NET 클라이언트에서 로깅은 `ConfigureLogging` 메서드를 이용해서 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-252">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="11e2a-253">로깅 공급자 및 필터는 서버와 동일한 방법으로 등록할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-253">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="11e2a-254">자세한 내용은 [ASP.NET Core 로깅](xref:fundamentals/logging/index) 설명서를 참고하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-254">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="11e2a-255">로깅 공급자를 등록하는 데 필요한 패키지를 설치해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-255">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="11e2a-256">전체 목록은 로깅 문서의 [기본 제공 로깅 공급자](xref:fundamentals/logging/index#built-in-logging-providers) 섹션을 참고하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-256">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="11e2a-257">예를 들어 콘솔 로깅을 사용하려면 `Microsoft.Extensions.Logging.Console` NuGet 패키지를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-257">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="11e2a-258">`AddConsole` 확장 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-258">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="11e2a-259">JavaScript 클라이언트에는 유사한 `configureLogging` 메서드가 존재합니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-259">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="11e2a-260">기록할 로그 메시지의 최소 수준을 나타내는 `LogLevel` 값을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-260">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="11e2a-261">로그는 브라우저 콘솔 창에 기록됩니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-261">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="11e2a-262">@No__t-0 값 대신 로그 수준 이름을 나타내는 `string` 값을 제공할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-262">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="11e2a-263">@No__t-0 상수에 액세스할 수 없는 환경에서 SignalR 로깅을 구성할 때 유용 합니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-263">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="11e2a-264">다음 표에서는 사용 가능한 로그 수준을 나열 합니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-264">The following table lists the available log levels.</span></span> <span data-ttu-id="11e2a-265">@No__t-0에 제공 하는 값은 기록 될 **최소** 로그 수준을 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-265">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="11e2a-266">이 수준에서 기록 되 **는 메시지 또는 테이블의 이후 나열 된 수준**에 기록 됩니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-266">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="11e2a-267">문자열</span><span class="sxs-lookup"><span data-stu-id="11e2a-267">String</span></span>                      | <span data-ttu-id="11e2a-268">LogLevel</span><span class="sxs-lookup"><span data-stu-id="11e2a-268">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="11e2a-269">`info` **or** `information`</span><span class="sxs-lookup"><span data-stu-id="11e2a-269">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="11e2a-270">`warn` **or** `warning`</span><span class="sxs-lookup"><span data-stu-id="11e2a-270">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

::: moniker-end

> [!NOTE]
> <span data-ttu-id="11e2a-271">로깅을 완전히 비활성화시키려면 `configureLogging` 메서드에서 `signalR.LogLevel.None`을 지정하십시오.</span><span class="sxs-lookup"><span data-stu-id="11e2a-271">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="11e2a-272">로깅에 대 한 자세한 내용은 [SignalR 진단 설명서](xref:signalr/diagnostics)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="11e2a-272">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="11e2a-273">SignalR Java 클라이언트는 [SLF4J](https://www.slf4j.org/) 라이브러리를 사용 하 여 로깅을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-273">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="11e2a-274">이는 라이브러리의 사용자가 특정 로깅 종속성을 가져와 자신의 특정 로깅 구현을 선택할 수 있게 해 주는 상위 수준 로깅 API입니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-274">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="11e2a-275">다음 코드 조각에서는 SignalR Java 클라이언트에서 `java.util.logging`을 사용 하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-275">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="11e2a-276">종속성에서 로깅을 구성 하지 않으면 SLF4J는 다음과 같은 경고 메시지와 함께 기본 작업 없음로 거를 로드 합니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-276">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="11e2a-277">이는 무시 해도 됩니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-277">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="11e2a-278">허용되는 전송 구성</span><span class="sxs-lookup"><span data-stu-id="11e2a-278">Configure allowed transports</span></span>

<span data-ttu-id="11e2a-279">SignalR에서 사용되는 전송은 `WithUrl` 호출에서 (JavaScript에서는 `withUrl` 호출에서) 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-279">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="11e2a-280">`HttpTransportType` 값들의 비트 OR을 이용해서 클라이언트가 지정한 전송만 사용하도록 제한할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-280">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="11e2a-281">기본적으로 모든 전송은 활성화되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-281">All transports are enabled by default.</span></span>

<span data-ttu-id="11e2a-282">예를 들어 다음 코드는 서버-전송 이벤트 전송을 비활성화시키고 WebSocket 및 롱 폴링 연결만 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-282">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="11e2a-283">JavaScript 클라이언트에서 전송은 `withUrl`에 제공되는 옵션 개체의 `transport` 필드를 설정하여 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-283">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="11e2a-284">이 버전의 Java 클라이언트 websocket은 유일 하 게 사용할 수 있는 전송입니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-284">In this version of the Java client websockets is the only available transport.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-3.0"

<span data-ttu-id="11e2a-285">Java 클라이언트에서 `HttpHubConnectionBuilder`에 `withTransport` 메서드를 사용 하 여 전송이 선택 됩니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-285">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="11e2a-286">Java 클라이언트는 기본적으로 Websocket 전송을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-286">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="11e2a-287">SignalR Java 클라이언트는 아직 전송 대체를 지원 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-287">The SignalR Java client doesn't support transport fallback yet.</span></span>

::: moniker-end

### <a name="configure-bearer-authentication"></a><span data-ttu-id="11e2a-288">전달자 인증 구성</span><span class="sxs-lookup"><span data-stu-id="11e2a-288">Configure bearer authentication</span></span>

<span data-ttu-id="11e2a-289">SignalR 요청과 함께 인증 데이터를 제공하려면 `AccessTokenProvider` 옵션(JavaScript에서는 `accessTokenFactory`를)을 이용해서 원하는 액세스 토큰을 반환하는 함수를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-289">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="11e2a-290">.NET 클라이언트에서는 액세스 토큰이 HTTP "Bearer Authentication" 토큰으로 전달됩니다(`Bearer` 형식의 `Authorization` 헤더를 통해).</span><span class="sxs-lookup"><span data-stu-id="11e2a-290">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="11e2a-291">JavaScript 클라이언트에서는 브라우저의 API가 헤더 적용 기능을 제한하는 몇 가지 경우를 **제외**하면 (특히 서버-전송 이벤트 및 WebSockets 요청) 액세스 토큰이 전달자 토큰으로 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-291">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="11e2a-292">이런 경우 액세스 토큰은 `access_token`이라는 쿼리 문자열 값으로 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-292">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="11e2a-293">.NET 클라이언트에서 `AccessTokenProvider` 옵션은 `WithUrl`에서 옵션 대리자를 이용해서 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-293">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="11e2a-294">JavaScript 클라이언트에서는 `withUrl`에서 옵션 개체의 `accessTokenFactory` 필드를 설정하여 액세스 토큰을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-294">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="11e2a-295">SignalR Java 클라이언트에서 [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)에 액세스 토큰 팩터리를 제공 하 여 인증에 사용할 전달자 토큰을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-295">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="11e2a-296">[WithAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) 를 사용 하 여 [rxjava](https://github.com/ReactiveX/RxJava) [Single @ no__t-3string >](https://reactivex.io/documentation/single.html)를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-296">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="11e2a-297">[단일. 지연](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)에 대 한 호출을 사용 하 여 클라이언트에 대 한 액세스 토큰을 생성 하는 논리를 작성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-297">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="11e2a-298">시간 제한 및 연결 유지 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="11e2a-298">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="11e2a-299">시간 제한 및 연결 유지 동작을 구성하기 위한 추가 옵션은 `HubConnection` 개체 자체에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-299">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>


::: moniker range=">= aspnetcore-2.2"

# <a name="nettabdotnet"></a>[<span data-ttu-id="11e2a-300">.NET</span><span class="sxs-lookup"><span data-stu-id="11e2a-300">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="11e2a-301">옵션</span><span class="sxs-lookup"><span data-stu-id="11e2a-301">Option</span></span> | <span data-ttu-id="11e2a-302">기본값</span><span class="sxs-lookup"><span data-stu-id="11e2a-302">Default value</span></span> | <span data-ttu-id="11e2a-303">설명</span><span class="sxs-lookup"><span data-stu-id="11e2a-303">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="11e2a-304">30초(30000밀리초)</span><span class="sxs-lookup"><span data-stu-id="11e2a-304">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="11e2a-305">서버 활동 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-305">Timeout for server activity.</span></span> <span data-ttu-id="11e2a-306">서버가 이 시간 제한 내에 메시지를 전송하지 않으면 클라이언트는 서버 연결이 끊어졌다고 간주하고 `Closed` 이벤트(JavaScript에서는 `onclose` 이벤트)를  트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-306">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="11e2a-307">이 값이 충분히 커야만 ping 메시지가 서버에서 전송**되고** 시간 제한 내에 클라이언트에 의해 수신될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-307">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="11e2a-308">권장 값은 ping이 도착할 시간을 허용 하기 위해 서버의 `KeepAliveInterval` 값을 두 번 이상 사용 하는 숫자입니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-308">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="11e2a-309">15초</span><span class="sxs-lookup"><span data-stu-id="11e2a-309">15 seconds</span></span> | <span data-ttu-id="11e2a-310">초기 서버 핸드셰이크에 대한 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-310">Timeout for initial server handshake.</span></span> <span data-ttu-id="11e2a-311">서버가 이 시간 제한 내에 핸드셰이크 응답을 전송하지 않으면 클라이언트는 핸드셰이크를 취소하고 `Closed` 이벤트(JavaScript에서는 `onclose` 이벤트)를 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-311">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="11e2a-312">이 설정은 심각한 네트워크 지연으로 인해 핸드셰이크 시간 제한 오류가 발생하는 경우에만 수정해야 하는 고급 설정입니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-312">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="11e2a-313">핸드셰이크 프로세스에 대한 보다 자세한 내용은 [SignalR 허브 프로토콜 사양](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)을 참고하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-313">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="11e2a-314">15초</span><span class="sxs-lookup"><span data-stu-id="11e2a-314">15 seconds</span></span> | <span data-ttu-id="11e2a-315">클라이언트에서 ping 메시지를 보내는 간격을 결정 합니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-315">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="11e2a-316">클라이언트에서 메시지를 보내면 타이머를 간격의 시작으로 다시 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-316">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="11e2a-317">클라이언트에서 서버에 설정 된 `ClientTimeoutInterval`의 메시지를 보내지 않은 경우 서버에서 클라이언트의 연결을 끊을 것으로 간주 합니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-317">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="11e2a-318">.NET 클라이언트에서 시간 제한 값은 `TimeSpan` 값으로 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-318">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascripttabjavascript"></a>[<span data-ttu-id="11e2a-319">JavaScript</span><span class="sxs-lookup"><span data-stu-id="11e2a-319">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="11e2a-320">옵션</span><span class="sxs-lookup"><span data-stu-id="11e2a-320">Option</span></span> | <span data-ttu-id="11e2a-321">기본값</span><span class="sxs-lookup"><span data-stu-id="11e2a-321">Default value</span></span> | <span data-ttu-id="11e2a-322">설명</span><span class="sxs-lookup"><span data-stu-id="11e2a-322">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="11e2a-323">30초(30000밀리초)</span><span class="sxs-lookup"><span data-stu-id="11e2a-323">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="11e2a-324">서버 활동 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-324">Timeout for server activity.</span></span> <span data-ttu-id="11e2a-325">서버가이 간격 내에 메시지를 보내지 않은 경우 클라이언트는 서버 연결을 끊고 `onclose` 이벤트를 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-325">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="11e2a-326">이 값이 충분히 커야만 ping 메시지가 서버에서 전송**되고** 시간 제한 내에 클라이언트에 의해 수신될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-326">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="11e2a-327">권장 값은 ping이 도착할 시간을 허용 하기 위해 서버의 `KeepAliveInterval` 값을 두 번 이상 사용 하는 숫자입니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-327">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="11e2a-328">15 초 (15000 밀리초)</span><span class="sxs-lookup"><span data-stu-id="11e2a-328">15 seconds (15,000 miliseconds)</span></span> | <span data-ttu-id="11e2a-329">클라이언트에서 ping 메시지를 보내는 간격을 결정 합니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-329">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="11e2a-330">클라이언트에서 메시지를 보내면 타이머를 간격의 시작으로 다시 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-330">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="11e2a-331">클라이언트에서 서버에 설정 된 `ClientTimeoutInterval`의 메시지를 보내지 않은 경우 서버에서 클라이언트의 연결을 끊을 것으로 간주 합니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-331">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="javatabjava"></a>[<span data-ttu-id="11e2a-332">Java</span><span class="sxs-lookup"><span data-stu-id="11e2a-332">Java</span></span>](#tab/java)

| <span data-ttu-id="11e2a-333">옵션</span><span class="sxs-lookup"><span data-stu-id="11e2a-333">Option</span></span> | <span data-ttu-id="11e2a-334">기본값</span><span class="sxs-lookup"><span data-stu-id="11e2a-334">Default value</span></span> | <span data-ttu-id="11e2a-335">설명</span><span class="sxs-lookup"><span data-stu-id="11e2a-335">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="11e2a-336">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="11e2a-336">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="11e2a-337">30초(30000밀리초)</span><span class="sxs-lookup"><span data-stu-id="11e2a-337">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="11e2a-338">서버 활동 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-338">Timeout for server activity.</span></span> <span data-ttu-id="11e2a-339">서버가이 간격 내에 메시지를 보내지 않은 경우 클라이언트는 서버 연결을 끊고 `onClose` 이벤트를 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-339">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="11e2a-340">이 값이 충분히 커야만 ping 메시지가 서버에서 전송**되고** 시간 제한 내에 클라이언트에 의해 수신될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-340">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="11e2a-341">권장 값은 ping이 도착할 시간을 허용 하기 위해 서버의 `KeepAliveInterval` 값을 두 번 이상 사용 하는 숫자입니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-341">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="11e2a-342">15초</span><span class="sxs-lookup"><span data-stu-id="11e2a-342">15 seconds</span></span> | <span data-ttu-id="11e2a-343">초기 서버 핸드셰이크에 대한 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-343">Timeout for initial server handshake.</span></span> <span data-ttu-id="11e2a-344">서버가이 간격 내에 핸드셰이크 응답을 보내지 않는 경우 클라이언트는 핸드셰이크를 취소 하 고 `onClose` 이벤트를 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-344">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="11e2a-345">이 설정은 심각한 네트워크 지연으로 인해 핸드셰이크 시간 제한 오류가 발생하는 경우에만 수정해야 하는 고급 설정입니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-345">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="11e2a-346">핸드셰이크 프로세스에 대한 자세한 내용은 [SignalR 허브 프로토콜 사양](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)을 참고하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-346">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="11e2a-347">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="11e2a-347">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="11e2a-348">15 초 (15000 밀리초)</span><span class="sxs-lookup"><span data-stu-id="11e2a-348">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="11e2a-349">클라이언트에서 ping 메시지를 보내는 간격을 결정 합니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-349">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="11e2a-350">클라이언트에서 메시지를 보내면 타이머를 간격의 시작으로 다시 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-350">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="11e2a-351">클라이언트에서 서버에 설정 된 `ClientTimeoutInterval`의 메시지를 보내지 않은 경우 서버에서 클라이언트의 연결을 끊을 것으로 간주 합니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-351">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

::: moniker-end

::: moniker range="< aspnetcore-2.2"

# <a name="nettabdotnet"></a>[<span data-ttu-id="11e2a-352">.NET</span><span class="sxs-lookup"><span data-stu-id="11e2a-352">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="11e2a-353">옵션</span><span class="sxs-lookup"><span data-stu-id="11e2a-353">Option</span></span> | <span data-ttu-id="11e2a-354">기본값</span><span class="sxs-lookup"><span data-stu-id="11e2a-354">Default value</span></span> | <span data-ttu-id="11e2a-355">설명</span><span class="sxs-lookup"><span data-stu-id="11e2a-355">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="11e2a-356">30초(30000밀리초)</span><span class="sxs-lookup"><span data-stu-id="11e2a-356">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="11e2a-357">서버 활동 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-357">Timeout for server activity.</span></span> <span data-ttu-id="11e2a-358">서버가 이 시간 제한 내에 메시지를 전송하지 않으면 클라이언트는 서버 연결이 끊어졌다고 간주하고 `Closed` 이벤트(JavaScript에서는 `onclose` 이벤트)를  트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-358">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="11e2a-359">이 값이 충분히 커야만 ping 메시지가 서버에서 전송**되고** 시간 제한 내에 클라이언트에 의해 수신될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-359">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="11e2a-360">권장 값은 ping이 도착할 시간을 허용 하기 위해 서버의 `KeepAliveInterval` 값을 두 번 이상 사용 하는 숫자입니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-360">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="11e2a-361">15초</span><span class="sxs-lookup"><span data-stu-id="11e2a-361">15 seconds</span></span> | <span data-ttu-id="11e2a-362">초기 서버 핸드셰이크에 대한 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-362">Timeout for initial server handshake.</span></span> <span data-ttu-id="11e2a-363">서버가 이 시간 제한 내에 핸드셰이크 응답을 전송하지 않으면 클라이언트는 핸드셰이크를 취소하고 `Closed` 이벤트(JavaScript에서는 `onclose` 이벤트)를 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-363">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="11e2a-364">이 설정은 심각한 네트워크 지연으로 인해 핸드셰이크 시간 제한 오류가 발생하는 경우에만 수정해야 하는 고급 설정입니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-364">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="11e2a-365">핸드셰이크 프로세스에 대한 보다 자세한 내용은 [SignalR 허브 프로토콜 사양](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)을 참고하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-365">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

<span data-ttu-id="11e2a-366">.NET 클라이언트에서 시간 제한 값은 `TimeSpan` 값으로 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-366">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascripttabjavascript"></a>[<span data-ttu-id="11e2a-367">JavaScript</span><span class="sxs-lookup"><span data-stu-id="11e2a-367">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="11e2a-368">옵션</span><span class="sxs-lookup"><span data-stu-id="11e2a-368">Option</span></span> | <span data-ttu-id="11e2a-369">기본값</span><span class="sxs-lookup"><span data-stu-id="11e2a-369">Default value</span></span> | <span data-ttu-id="11e2a-370">설명</span><span class="sxs-lookup"><span data-stu-id="11e2a-370">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="11e2a-371">30초(30000밀리초)</span><span class="sxs-lookup"><span data-stu-id="11e2a-371">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="11e2a-372">서버 활동 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-372">Timeout for server activity.</span></span> <span data-ttu-id="11e2a-373">서버가이 간격 내에 메시지를 보내지 않은 경우 클라이언트는 서버 연결을 끊고 `onclose` 이벤트를 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-373">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="11e2a-374">이 값이 충분히 커야만 ping 메시지가 서버에서 전송**되고** 시간 제한 내에 클라이언트에 의해 수신될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-374">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="11e2a-375">권장 값은 ping이 도착할 시간을 허용 하기 위해 서버의 `KeepAliveInterval` 값을 두 번 이상 사용 하는 숫자입니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-375">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |

# <a name="javatabjava"></a>[<span data-ttu-id="11e2a-376">Java</span><span class="sxs-lookup"><span data-stu-id="11e2a-376">Java</span></span>](#tab/java)

| <span data-ttu-id="11e2a-377">옵션</span><span class="sxs-lookup"><span data-stu-id="11e2a-377">Option</span></span> | <span data-ttu-id="11e2a-378">기본값</span><span class="sxs-lookup"><span data-stu-id="11e2a-378">Default value</span></span> | <span data-ttu-id="11e2a-379">설명</span><span class="sxs-lookup"><span data-stu-id="11e2a-379">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="11e2a-380">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="11e2a-380">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="11e2a-381">30초(30000밀리초)</span><span class="sxs-lookup"><span data-stu-id="11e2a-381">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="11e2a-382">서버 활동 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-382">Timeout for server activity.</span></span> <span data-ttu-id="11e2a-383">서버가이 간격 내에 메시지를 보내지 않은 경우 클라이언트는 서버 연결을 끊고 `onClose` 이벤트를 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-383">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="11e2a-384">이 값이 충분히 커야만 ping 메시지가 서버에서 전송**되고** 시간 제한 내에 클라이언트에 의해 수신될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-384">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="11e2a-385">권장되는 값은 ping이 도착할 시간을 확보하기 위해 최소한 서버의 `KeepAliveInterval` 값의 두 배가 되는 숫자여야 합니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-385">The recommended value is a number at least double the server's `KeepAliveInterval` value, to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="11e2a-386">15초</span><span class="sxs-lookup"><span data-stu-id="11e2a-386">15 seconds</span></span> | <span data-ttu-id="11e2a-387">초기 서버 핸드셰이크에 대한 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-387">Timeout for initial server handshake.</span></span> <span data-ttu-id="11e2a-388">서버가이 간격 내에 핸드셰이크 응답을 보내지 않는 경우 클라이언트는 핸드셰이크를 취소 하 고 `onClose` 이벤트를 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-388">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="11e2a-389">이 설정은 심각한 네트워크 지연으로 인해 핸드셰이크 시간 제한 오류가 발생하는 경우에만 수정해야 하는 고급 설정입니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-389">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="11e2a-390">핸드셰이크 프로세스에 대한 보다 자세한 내용은 [SignalR 허브 프로토콜 사양](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)을 참고하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-390">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

::: moniker-end

---

### <a name="configure-additional-options"></a><span data-ttu-id="11e2a-391">추가 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="11e2a-391">Configure additional options</span></span>

<span data-ttu-id="11e2a-392">추가 옵션은 `HubConnectionBuilder` 또는 Java 클라이언트의 `HttpHubConnectionBuilder`에 있는 다양 한 구성 Api에서 `WithUrl` (JavaScript의 `withUrl`) 방법으로 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-392">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="nettabdotnet"></a>[<span data-ttu-id="11e2a-393">.NET</span><span class="sxs-lookup"><span data-stu-id="11e2a-393">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="11e2a-394">.NET 옵션</span><span class="sxs-lookup"><span data-stu-id="11e2a-394">.NET Option</span></span> |  <span data-ttu-id="11e2a-395">기본값</span><span class="sxs-lookup"><span data-stu-id="11e2a-395">Default value</span></span> | <span data-ttu-id="11e2a-396">설명</span><span class="sxs-lookup"><span data-stu-id="11e2a-396">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="11e2a-397">HTTP 요청에서 전달자 인증 토큰으로 제공된 문자열을 반환하는 함수입니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-397">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="11e2a-398">이 값을 `true`로 설정하면 협상 단계를 건너뜁니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-398">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="11e2a-399">**WebSockets 전송이 유일하게 활성화된 전송인 경우에만 지원됩니다.**</span><span class="sxs-lookup"><span data-stu-id="11e2a-399">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="11e2a-400">Azure SignalR Service를 사용하는 경우에는 이 설정을 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-400">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="11e2a-401">비어 있음</span><span class="sxs-lookup"><span data-stu-id="11e2a-401">Empty</span></span> | <span data-ttu-id="11e2a-402">요청을 인증하기 위해 전송할 TLS 인증서의 컬렉션입니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-402">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="11e2a-403">비어 있음</span><span class="sxs-lookup"><span data-stu-id="11e2a-403">Empty</span></span> | <span data-ttu-id="11e2a-404">모든 HTTP 요청과 함께 전송할 HTTP 쿠키의 컬렉션입니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-404">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="11e2a-405">비어 있음</span><span class="sxs-lookup"><span data-stu-id="11e2a-405">Empty</span></span> | <span data-ttu-id="11e2a-406">모든 HTTP 요청과 함께 전송할 자격 증명입니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-406">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="11e2a-407">5초</span><span class="sxs-lookup"><span data-stu-id="11e2a-407">5 seconds</span></span> | <span data-ttu-id="11e2a-408">Websocket에만 해당됩니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-408">WebSockets only.</span></span> <span data-ttu-id="11e2a-409">클라이언트가 서버를 닫은 후 닫기 요청을 확인하기 위해 대기하는 최대 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-409">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="11e2a-410">서버가 이 시간 내에 종료를 승인하지 않으면 클라이언트가 연결을 끊습니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-410">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="11e2a-411">비어 있음</span><span class="sxs-lookup"><span data-stu-id="11e2a-411">Empty</span></span> | <span data-ttu-id="11e2a-412">모든 HTTP 요청과 함께 보낼 추가 HTTP 헤더의 맵입니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-412">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="11e2a-413">HTTP 요청을 전송하기 위해 사용되는 `HttpMessageHandler`를 구성하거나 대체하는 데 사용할 수 있는 대리자입니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-413">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="11e2a-414">WebSocket 연결에는 사용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-414">Not used for WebSocket connections.</span></span> <span data-ttu-id="11e2a-415">이 대리자는 null이 아닌 값을 반환해야 하며 매개 변수로 기본값을 받습니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-415">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="11e2a-416">이 기본값의 설정을 수정하여 반환하거나 새로운 `HttpMessageHandler` 인스턴스를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-416">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="11e2a-417">**처리기를 대체할 때 제공 된 처리기에서 유지할 설정을 복사 해야 합니다. 그렇지 않으면 구성 된 옵션 (예: 쿠키 및 헤더)이 새 처리기에 적용 되지 않습니다.**</span><span class="sxs-lookup"><span data-stu-id="11e2a-417">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="11e2a-418">HTTP 요청을 전송할 때 사용할 HTTP 프록시입니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-418">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="11e2a-419">HTTP 및 WebSockets 요청에 대한 기본 자격 증명을 전송하려면 이 부울 값을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-419">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="11e2a-420">그러면 Windows 인증을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-420">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="11e2a-421">추가적인 WebSocket 옵션을 구성할 수 있는 대리자입니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-421">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="11e2a-422">옵션을 구성에 사용할 수 있는 [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions)의 인스턴스를 전달받습니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-422">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascripttabjavascript"></a>[<span data-ttu-id="11e2a-423">JavaScript</span><span class="sxs-lookup"><span data-stu-id="11e2a-423">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="11e2a-424">JavaScript 옵션</span><span class="sxs-lookup"><span data-stu-id="11e2a-424">JavaScript Option</span></span> | <span data-ttu-id="11e2a-425">Default Value</span><span class="sxs-lookup"><span data-stu-id="11e2a-425">Default Value</span></span> | <span data-ttu-id="11e2a-426">설명</span><span class="sxs-lookup"><span data-stu-id="11e2a-426">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="11e2a-427">HTTP 요청에서 전달자 인증 토큰으로 제공된 문자열을 반환하는 함수입니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-427">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="11e2a-428">이 값을 `true`로 설정하면 협상 단계를 건너뜁니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-428">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="11e2a-429">**WebSockets 전송이 유일하게 활성화된 전송인 경우에만 지원됩니다.**</span><span class="sxs-lookup"><span data-stu-id="11e2a-429">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="11e2a-430">Azure SignalR Service를 사용하는 경우에는 이 설정을 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-430">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="javatabjava"></a>[<span data-ttu-id="11e2a-431">Java</span><span class="sxs-lookup"><span data-stu-id="11e2a-431">Java</span></span>](#tab/java)

| <span data-ttu-id="11e2a-432">Java 옵션</span><span class="sxs-lookup"><span data-stu-id="11e2a-432">Java Option</span></span> | <span data-ttu-id="11e2a-433">Default Value</span><span class="sxs-lookup"><span data-stu-id="11e2a-433">Default Value</span></span> | <span data-ttu-id="11e2a-434">설명</span><span class="sxs-lookup"><span data-stu-id="11e2a-434">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="11e2a-435">HTTP 요청에서 전달자 인증 토큰으로 제공된 문자열을 반환하는 함수입니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-435">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="11e2a-436">이 값을 `true`로 설정하면 협상 단계를 건너뜁니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-436">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="11e2a-437">**WebSockets 전송이 유일하게 활성화된 전송인 경우에만 지원됩니다.**</span><span class="sxs-lookup"><span data-stu-id="11e2a-437">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="11e2a-438">Azure SignalR Service를 사용하는 경우에는 이 설정을 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-438">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="11e2a-439">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="11e2a-439">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="11e2a-440">비어 있음</span><span class="sxs-lookup"><span data-stu-id="11e2a-440">Empty</span></span> | <span data-ttu-id="11e2a-441">모든 HTTP 요청과 함께 보낼 추가 HTTP 헤더의 맵입니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-441">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="11e2a-442">.NET 클라이언트에서 이 옵션들은 `WithUrl`에 제공되는 옵션 대리자를 이용해서 수정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-442">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="11e2a-443">JavaScript 클라이언트에서 이 옵션들은 `withUrl`에 제공되는 JavaScript 개체를 통해서 제공될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-443">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="11e2a-444">Java 클라이언트에서 이러한 옵션은 `HubConnectionBuilder.create("HUB URL")`에서 반환 된 @no__t에서 반환 된 메서드를 사용 하 여 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="11e2a-444">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="11e2a-445">추가 자료</span><span class="sxs-lookup"><span data-stu-id="11e2a-445">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>
