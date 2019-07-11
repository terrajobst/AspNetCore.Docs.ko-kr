---
title: ASP.NET Core SignalR 구성
author: bradygaster
description: ASP.NET Core SignalR 앱을 구성하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 06/03/2019
uid: signalr/configuration
ms.openlocfilehash: 8c9fcaecb04555718f5da6a42a8e56c258e795af
ms.sourcegitcommit: 8516b586541e6ba402e57228e356639b85dfb2b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67813447"
---
# <a name="aspnet-core-signalr-configuration"></a><span data-ttu-id="fdad5-103">ASP.NET Core SignalR 구성</span><span class="sxs-lookup"><span data-stu-id="fdad5-103">ASP.NET Core SignalR configuration</span></span>

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="fdad5-104">JSON/MessagePack 직렬화 옵션</span><span class="sxs-lookup"><span data-stu-id="fdad5-104">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="fdad5-105">ASP.NET Core SignalR 메시지 인코딩에 대 한 두 가지 프로토콜을 지원 합니다. [JSON](https://www.json.org/) 하 고 [MessagePack](https://msgpack.org/index.html)합니다.</span><span class="sxs-lookup"><span data-stu-id="fdad5-105">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="fdad5-106">각 프로토콜에 serialization 구성 옵션이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fdad5-106">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="fdad5-107">JSON serialization을 사용 하 여 서버에 구성할 수 있습니다 합니다 [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) 후 추가할 수 있는 확장 메서드 [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) 에서 프로그램 `Startup.ConfigureServices` 메서드.</span><span class="sxs-lookup"><span data-stu-id="fdad5-107">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="fdad5-108">`AddJsonProtocol` 메서드는 `options` 개체를 전달받는 대리자를 받습니다.</span><span class="sxs-lookup"><span data-stu-id="fdad5-108">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="fdad5-109">이 개체의 [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) 속성은 JSON.NET의 `JsonSerializerSettings` 개체로, 인수 및 반환 값의 직렬화를 구성하는 데 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fdad5-109">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="fdad5-110">더 자세한 내용은 [JSON.NET 설명서](https://www.newtonsoft.com/json/help/html/Introduction.htm)를 참고하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="fdad5-110">See the [JSON.NET Documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm) for more details.</span></span>

<span data-ttu-id="fdad5-111">예를 들어 기본 값인 "camelCase" 속성 이름 대신 "PascalCase" 속성 이름을 사용하도록 직렬화 변환기를 구성하려면 다음 코드를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="fdad5-111">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
        new DefaultContractResolver();
    });
```

<span data-ttu-id="fdad5-112">.NET 클라이언트에서는 [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)에 동일한 `AddJsonProtocol` 확장 메서드가 존재합니다.</span><span class="sxs-lookup"><span data-stu-id="fdad5-112">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="fdad5-113">이 확장 메서드를 확인하려면 `Microsoft.Extensions.DependencyInjection` 네임스페이스를 임포트해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="fdad5-113">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="fdad5-114">현재 JavaScript 클라이언트에서는 JSON 직렬화를 구성할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="fdad5-114">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="fdad5-115">MessagePack 직렬화 옵션</span><span class="sxs-lookup"><span data-stu-id="fdad5-115">MessagePack serialization options</span></span>

<span data-ttu-id="fdad5-116">MessagePack 직렬화는 [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) 호출에 대리자를 제공하여 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fdad5-116">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="fdad5-117">더 자세한 내용은 [SignalR의 MessagePack](xref:signalr/messagepackhubprotocol)을 참고하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="fdad5-117">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="fdad5-118">현재 JavaScript 클라이언트에서는 MessagePack 직렬화를 구성할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="fdad5-118">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="fdad5-119">서버 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="fdad5-119">Configure server options</span></span>

<span data-ttu-id="fdad5-120">다음 표는 SignalR 허브를 구성하기 위한 옵션을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="fdad5-120">The following table describes options for configuring SignalR hubs:</span></span>

::: moniker range=">= aspnetcore-3.0"

| <span data-ttu-id="fdad5-121">옵션</span><span class="sxs-lookup"><span data-stu-id="fdad5-121">Option</span></span> | <span data-ttu-id="fdad5-122">기본값</span><span class="sxs-lookup"><span data-stu-id="fdad5-122">Default Value</span></span> | <span data-ttu-id="fdad5-123">설명</span><span class="sxs-lookup"><span data-stu-id="fdad5-123">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="fdad5-124">30초</span><span class="sxs-lookup"><span data-stu-id="fdad5-124">30 seconds</span></span> | <span data-ttu-id="fdad5-125">서버는 클라이언트를 고려 하는 경우이 간격 등 연결 유지 메시지를 수신 하지 않은 연결 끊김.</span><span class="sxs-lookup"><span data-stu-id="fdad5-125">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="fdad5-126">실제로이 구현 하는 방법으로 인해 연결이 끊어진 표시할 클라이언트에 대 한이 시간 제한 간격 보다 오래 걸릴 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fdad5-126">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="fdad5-127">권장된 값은 double을 `KeepAliveInterval` 값입니다.</span><span class="sxs-lookup"><span data-stu-id="fdad5-127">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="fdad5-128">15초</span><span class="sxs-lookup"><span data-stu-id="fdad5-128">15 seconds</span></span> | <span data-ttu-id="fdad5-129">클라이언트가 이 시간 제한 내에 초기 핸드셰이크 메시지를 전송하지 않으면 연결이 닫힙니다.</span><span class="sxs-lookup"><span data-stu-id="fdad5-129">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="fdad5-130">이 설정은 심각한 네트워크 지연으로 인해 핸드셰이크 시간 제한 오류가 발생하는 경우에만 수정해야 하는 고급 설정입니다.</span><span class="sxs-lookup"><span data-stu-id="fdad5-130">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="fdad5-131">핸드셰이크 프로세스에 대한 보다 자세한 내용은 [SignalR 허브 프로토콜 사양](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)을 참고하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="fdad5-131">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="fdad5-132">15초</span><span class="sxs-lookup"><span data-stu-id="fdad5-132">15 seconds</span></span> | <span data-ttu-id="fdad5-133">서버가 이 간격 내에 메시지를 전송하지 않으면 자동으로 ping 메시지가 전송되어 연결이 열린 상태로 유지됩니다.</span><span class="sxs-lookup"><span data-stu-id="fdad5-133">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="fdad5-134">`KeepAliveInterval`을 변경할 경우 클라이언트의 `ServerTimeout`/`serverTimeoutInMilliseconds` 설정도 변경하십시오.</span><span class="sxs-lookup"><span data-stu-id="fdad5-134">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="fdad5-135">권장되는 `ServerTimeout`/`serverTimeoutInMilliseconds` 값은 `KeepAliveInterval` 값의 두 배입니다.</span><span class="sxs-lookup"><span data-stu-id="fdad5-135">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="fdad5-136">설치된 모든 프로토콜</span><span class="sxs-lookup"><span data-stu-id="fdad5-136">All installed protocols</span></span> | <span data-ttu-id="fdad5-137">허브가 지원하는 프로토콜입니다.</span><span class="sxs-lookup"><span data-stu-id="fdad5-137">Protocols supported by this hub.</span></span> <span data-ttu-id="fdad5-138">기본적으로 서버에 등록된 모든 프로토콜이 허용되지만 이 목록에서 프로토콜을 제거하여 개별 허브에 대해 특정 프로토콜을 비활성화시킬 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fdad5-138">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="fdad5-139">이 값이 `true`면 Hub 메서드에서 예외가 발생할 경우 자세한 예외 메시지가 클라이언트로 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="fdad5-139">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="fdad5-140">예외 메시지에는 민감한 정보가 포함되어 있을 수 있으므로 기본값은 `false`입니다.</span><span class="sxs-lookup"><span data-stu-id="fdad5-140">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="fdad5-141">클라이언트에 대 한 버퍼링 할 수 있는 항목의 최대 수에 스트림을 업로드 합니다.</span><span class="sxs-lookup"><span data-stu-id="fdad5-141">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="fdad5-142">이 한도 도달 하는 경우 호출을 처리 하는 서버 스트림 항목을 처리할 때까지 차단 됩니다.</span><span class="sxs-lookup"><span data-stu-id="fdad5-142">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|

::: moniker-end

::: moniker range="= aspnetcore-2.2"

| <span data-ttu-id="fdad5-143">옵션</span><span class="sxs-lookup"><span data-stu-id="fdad5-143">Option</span></span> | <span data-ttu-id="fdad5-144">기본값</span><span class="sxs-lookup"><span data-stu-id="fdad5-144">Default Value</span></span> | <span data-ttu-id="fdad5-145">Description</span><span class="sxs-lookup"><span data-stu-id="fdad5-145">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="fdad5-146">30초</span><span class="sxs-lookup"><span data-stu-id="fdad5-146">30 seconds</span></span> | <span data-ttu-id="fdad5-147">서버는 클라이언트를 고려 하는 경우이 간격 등 연결 유지 메시지를 수신 하지 않은 연결 끊김.</span><span class="sxs-lookup"><span data-stu-id="fdad5-147">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="fdad5-148">실제로이 구현 하는 방법으로 인해 연결이 끊어진 표시할 클라이언트에 대 한이 시간 제한 간격 보다 오래 걸릴 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fdad5-148">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="fdad5-149">권장된 값은 double을 `KeepAliveInterval` 값입니다.</span><span class="sxs-lookup"><span data-stu-id="fdad5-149">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="fdad5-150">15초</span><span class="sxs-lookup"><span data-stu-id="fdad5-150">15 seconds</span></span> | <span data-ttu-id="fdad5-151">클라이언트가 이 시간 제한 내에 초기 핸드셰이크 메시지를 전송하지 않으면 연결이 닫힙니다.</span><span class="sxs-lookup"><span data-stu-id="fdad5-151">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="fdad5-152">이 설정은 심각한 네트워크 지연으로 인해 핸드셰이크 시간 제한 오류가 발생하는 경우에만 수정해야 하는 고급 설정입니다.</span><span class="sxs-lookup"><span data-stu-id="fdad5-152">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="fdad5-153">핸드셰이크 프로세스에 대한 보다 자세한 내용은 [SignalR 허브 프로토콜 사양](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)을 참고하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="fdad5-153">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="fdad5-154">15초</span><span class="sxs-lookup"><span data-stu-id="fdad5-154">15 seconds</span></span> | <span data-ttu-id="fdad5-155">서버가 이 간격 내에 메시지를 전송하지 않으면 자동으로 ping 메시지가 전송되어 연결이 열린 상태로 유지됩니다.</span><span class="sxs-lookup"><span data-stu-id="fdad5-155">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="fdad5-156">`KeepAliveInterval`을 변경할 경우 클라이언트의 `ServerTimeout`/`serverTimeoutInMilliseconds` 설정도 변경하십시오.</span><span class="sxs-lookup"><span data-stu-id="fdad5-156">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="fdad5-157">권장되는 `ServerTimeout`/`serverTimeoutInMilliseconds` 값은 `KeepAliveInterval` 값의 두 배입니다.</span><span class="sxs-lookup"><span data-stu-id="fdad5-157">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="fdad5-158">설치된 모든 프로토콜</span><span class="sxs-lookup"><span data-stu-id="fdad5-158">All installed protocols</span></span> | <span data-ttu-id="fdad5-159">허브가 지원하는 프로토콜입니다.</span><span class="sxs-lookup"><span data-stu-id="fdad5-159">Protocols supported by this hub.</span></span> <span data-ttu-id="fdad5-160">기본적으로 서버에 등록된 모든 프로토콜이 허용되지만 이 목록에서 프로토콜을 제거하여 개별 허브에 대해 특정 프로토콜을 비활성화시킬 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fdad5-160">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="fdad5-161">이 값이 `true`면 Hub 메서드에서 예외가 발생할 경우 자세한 예외 메시지가 클라이언트로 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="fdad5-161">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="fdad5-162">예외 메시지에는 민감한 정보가 포함되어 있을 수 있으므로 기본값은 `false`입니다.</span><span class="sxs-lookup"><span data-stu-id="fdad5-162">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

::: moniker-end

::: moniker range="= aspnetcore-2.1"

| <span data-ttu-id="fdad5-163">옵션</span><span class="sxs-lookup"><span data-stu-id="fdad5-163">Option</span></span> | <span data-ttu-id="fdad5-164">기본값</span><span class="sxs-lookup"><span data-stu-id="fdad5-164">Default Value</span></span> | <span data-ttu-id="fdad5-165">설명</span><span class="sxs-lookup"><span data-stu-id="fdad5-165">Description</span></span> |
| ------ | ------------- | ----------- |
| `HandshakeTimeout` | <span data-ttu-id="fdad5-166">15초</span><span class="sxs-lookup"><span data-stu-id="fdad5-166">15 seconds</span></span> | <span data-ttu-id="fdad5-167">클라이언트가 이 시간 제한 내에 초기 핸드셰이크 메시지를 전송하지 않으면 연결이 닫힙니다.</span><span class="sxs-lookup"><span data-stu-id="fdad5-167">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="fdad5-168">이 설정은 심각한 네트워크 지연으로 인해 핸드셰이크 시간 제한 오류가 발생하는 경우에만 수정해야 하는 고급 설정입니다.</span><span class="sxs-lookup"><span data-stu-id="fdad5-168">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="fdad5-169">핸드셰이크 프로세스에 대한 보다 자세한 내용은 [SignalR 허브 프로토콜 사양](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)을 참고하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="fdad5-169">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="fdad5-170">15초</span><span class="sxs-lookup"><span data-stu-id="fdad5-170">15 seconds</span></span> | <span data-ttu-id="fdad5-171">서버가 이 간격 내에 메시지를 전송하지 않으면 자동으로 ping 메시지가 전송되어 연결이 열린 상태로 유지됩니다.</span><span class="sxs-lookup"><span data-stu-id="fdad5-171">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="fdad5-172">`KeepAliveInterval`을 변경할 경우 클라이언트의 `ServerTimeout`/`serverTimeoutInMilliseconds` 설정도 변경하십시오.</span><span class="sxs-lookup"><span data-stu-id="fdad5-172">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="fdad5-173">권장되는 `ServerTimeout`/`serverTimeoutInMilliseconds` 값은 `KeepAliveInterval` 값의 두 배입니다.</span><span class="sxs-lookup"><span data-stu-id="fdad5-173">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="fdad5-174">설치된 모든 프로토콜</span><span class="sxs-lookup"><span data-stu-id="fdad5-174">All installed protocols</span></span> | <span data-ttu-id="fdad5-175">허브가 지원하는 프로토콜입니다.</span><span class="sxs-lookup"><span data-stu-id="fdad5-175">Protocols supported by this hub.</span></span> <span data-ttu-id="fdad5-176">기본적으로 서버에 등록된 모든 프로토콜이 허용되지만 이 목록에서 프로토콜을 제거하여 개별 허브에 대해 특정 프로토콜을 비활성화시킬 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fdad5-176">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="fdad5-177">이 값이 `true`면 Hub 메서드에서 예외가 발생할 경우 자세한 예외 메시지가 클라이언트로 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="fdad5-177">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="fdad5-178">예외 메시지에는 민감한 정보가 포함되어 있을 수 있으므로 기본값은 `false`입니다.</span><span class="sxs-lookup"><span data-stu-id="fdad5-178">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

::: moniker-end

<span data-ttu-id="fdad5-179">`Startup.ConfigureServices`에서 `AddSignalR` 호출에 옵션 대리자를 제공하여 모든 허브에 대한 옵션을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fdad5-179">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="fdad5-180">단일 허브에 대 한 옵션에서 제공 하는 전역 옵션을 재정의 `AddSignalR` 를 사용 하 여 구성할 수 있습니다 및 <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span><span class="sxs-lookup"><span data-stu-id="fdad5-180">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="fdad5-181">고급 HTTP 구성 옵션</span><span class="sxs-lookup"><span data-stu-id="fdad5-181">Advanced HTTP configuration options</span></span>

<span data-ttu-id="fdad5-182">전송 및 메모리 버퍼 관리와 관련된 고급 설정을 구성하려면 `HttpConnectionDispatcherOptions`를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="fdad5-182">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="fdad5-183">이러한 옵션에 대 한 대리자를 전달 하 여 구성 됩니다 [MapHub\<T >](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) 에서 `Startup.Configure`합니다.</span><span class="sxs-lookup"><span data-stu-id="fdad5-183">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="fdad5-184">다음 표에서 ASP.NET Core SignalR의 고급 HTTP 옵션을 구성 하는 것에 대 한 옵션을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="fdad5-184">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="fdad5-185">옵션</span><span class="sxs-lookup"><span data-stu-id="fdad5-185">Option</span></span> | <span data-ttu-id="fdad5-186">기본값</span><span class="sxs-lookup"><span data-stu-id="fdad5-186">Default Value</span></span> | <span data-ttu-id="fdad5-187">설명</span><span class="sxs-lookup"><span data-stu-id="fdad5-187">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="fdad5-188">32KB</span><span class="sxs-lookup"><span data-stu-id="fdad5-188">32 KB</span></span> | <span data-ttu-id="fdad5-189">클라이언트로부터 수신하는 서버 버퍼의 최대 바이트 수입니다.</span><span class="sxs-lookup"><span data-stu-id="fdad5-189">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="fdad5-190">이 값을 늘리면 서버가 더 큰 메시지를 수신할 수 있지만 메모리 소비에 부정적인 영향을 줄 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fdad5-190">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="fdad5-191">허브 클래스에 적용된 `Authorize` 특성에서 자동으로 수집된 데이터입니다.</span><span class="sxs-lookup"><span data-stu-id="fdad5-191">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="fdad5-192">클라이언트가 허브에 연결할 권한이 있는지 확인하는 데 사용되는 [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) 개체의 목록입니다.</span><span class="sxs-lookup"><span data-stu-id="fdad5-192">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="fdad5-193">32KB</span><span class="sxs-lookup"><span data-stu-id="fdad5-193">32 KB</span></span> | <span data-ttu-id="fdad5-194">앱에서 전송하는 서버 버퍼의 최대 바이트 수입니다.</span><span class="sxs-lookup"><span data-stu-id="fdad5-194">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="fdad5-195">이 값을 늘리면 서버가 더 큰 메시지를 전송할 수 있지만 메모리 소비에 부정적인 영향을 줄 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fdad5-195">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="fdad5-196">모든 전송을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fdad5-196">All Transports are enabled.</span></span> | <span data-ttu-id="fdad5-197">클라이언트가 연결에 사용할 수 있는 전송을 제한할 수 있는 `HttpTransportType` 값들의 비트 마스크입니다.</span><span class="sxs-lookup"><span data-stu-id="fdad5-197">A bitmask of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="fdad5-198">아래 내용을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="fdad5-198">See below.</span></span> | <span data-ttu-id="fdad5-199">롱 폴링 전송과 관련된 추가 옵션입니다.</span><span class="sxs-lookup"><span data-stu-id="fdad5-199">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="fdad5-200">아래 내용을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="fdad5-200">See below.</span></span> | <span data-ttu-id="fdad5-201">WebSockets 전송과 관련된 추가 옵션입니다.</span><span class="sxs-lookup"><span data-stu-id="fdad5-201">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="fdad5-202">롱 폴링 전송에는 `LongPolling` 속성을 이용해서 구성할 수 있는 추가적인 옵션이 존재합니다.</span><span class="sxs-lookup"><span data-stu-id="fdad5-202">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="fdad5-203">옵션</span><span class="sxs-lookup"><span data-stu-id="fdad5-203">Option</span></span> | <span data-ttu-id="fdad5-204">기본값</span><span class="sxs-lookup"><span data-stu-id="fdad5-204">Default Value</span></span> | <span data-ttu-id="fdad5-205">설명</span><span class="sxs-lookup"><span data-stu-id="fdad5-205">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="fdad5-206">90초</span><span class="sxs-lookup"><span data-stu-id="fdad5-206">90 seconds</span></span> | <span data-ttu-id="fdad5-207">단일 폴링 요청을 종료하기 전에 서버가 메시지를 클라이언트에 전송할 때까지 대기하는 최대 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="fdad5-207">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="fdad5-208">이 값을 줄이면 클라이언트는 더 자주 새 폴링 요청을 발행합니다.</span><span class="sxs-lookup"><span data-stu-id="fdad5-208">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="fdad5-209">WebSocket 전송에는 `WebSockets` 속성을 이용해서 구성할 수 있는 추가적인 옵션이 존재합니다.</span><span class="sxs-lookup"><span data-stu-id="fdad5-209">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="fdad5-210">옵션</span><span class="sxs-lookup"><span data-stu-id="fdad5-210">Option</span></span> | <span data-ttu-id="fdad5-211">기본값</span><span class="sxs-lookup"><span data-stu-id="fdad5-211">Default Value</span></span> | <span data-ttu-id="fdad5-212">설명</span><span class="sxs-lookup"><span data-stu-id="fdad5-212">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="fdad5-213">5초</span><span class="sxs-lookup"><span data-stu-id="fdad5-213">5 seconds</span></span> | <span data-ttu-id="fdad5-214">서버가 닫힌 후 클라이언트가 이 시간 제한 내에 닫기에 실패하면 연결이 종료됩니다.</span><span class="sxs-lookup"><span data-stu-id="fdad5-214">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="fdad5-215">사용자 지정 값으로 `Sec-WebSocket-Protocol` 헤더를 설정하기 위해 사용할 수 있는 대리자입니다.</span><span class="sxs-lookup"><span data-stu-id="fdad5-215">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="fdad5-216">이 대리자는 클라이언트에서 요청한 값을 입력으로 받아서 원하는 값을 반환해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="fdad5-216">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="fdad5-217">클라이언트 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="fdad5-217">Configure client options</span></span>

<span data-ttu-id="fdad5-218">클라이언트 옵션에서 구성할 수 있습니다는 `HubConnectionBuilder` 형식 (.NET 및 JavaScript 클라이언트에서 사용 가능).</span><span class="sxs-lookup"><span data-stu-id="fdad5-218">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="fdad5-219">Java 클라이언트에서 사용할 수 있는 것도 있지만 `HttpHubConnectionBuilder` 하위 클래스는 작성기 구성 옵션을 on으로 포함 무엇을 `HubConnection` 자체입니다.</span><span class="sxs-lookup"><span data-stu-id="fdad5-219">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="fdad5-220">로깅 구성</span><span class="sxs-lookup"><span data-stu-id="fdad5-220">Configure logging</span></span>

<span data-ttu-id="fdad5-221">.NET 클라이언트에서 로깅은 `ConfigureLogging` 메서드를 이용해서 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="fdad5-221">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="fdad5-222">로깅 공급자 및 필터는 서버와 동일한 방법으로 등록할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fdad5-222">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="fdad5-223">자세한 내용은 [ASP.NET Core 로깅](xref:fundamentals/logging/index) 설명서를 참고하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="fdad5-223">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="fdad5-224">로깅 공급자를 등록하는 데 필요한 패키지를 설치해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="fdad5-224">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="fdad5-225">전체 목록은 로깅 문서의 [기본 제공 로깅 공급자](xref:fundamentals/logging/index#built-in-logging-providers) 섹션을 참고하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="fdad5-225">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="fdad5-226">예를 들어 콘솔 로깅을 사용하려면 `Microsoft.Extensions.Logging.Console` NuGet 패키지를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="fdad5-226">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="fdad5-227">`AddConsole` 확장 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="fdad5-227">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="fdad5-228">JavaScript 클라이언트에는 유사한 `configureLogging` 메서드가 존재합니다.</span><span class="sxs-lookup"><span data-stu-id="fdad5-228">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="fdad5-229">기록할 로그 메시지의 최소 수준을 나타내는 `LogLevel` 값을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="fdad5-229">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="fdad5-230">로그는 브라우저 콘솔 창에 기록됩니다.</span><span class="sxs-lookup"><span data-stu-id="fdad5-230">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="fdad5-231">대신에 `LogLevel` 값을 제공할 수도 있습니다는 `string` 로그 수준 이름을 나타내는 값입니다.</span><span class="sxs-lookup"><span data-stu-id="fdad5-231">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="fdad5-232">구성 환경에서 로깅 SignalR에 대 한 액세스 없는 경우에 유용 합니다 `LogLevel` 상수입니다.</span><span class="sxs-lookup"><span data-stu-id="fdad5-232">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="fdad5-233">다음 표에서 사용 가능한 로그 수준은 나열합니다.</span><span class="sxs-lookup"><span data-stu-id="fdad5-233">The following table lists the available log levels.</span></span> <span data-ttu-id="fdad5-234">에 제공한 값 `configureLogging` 설정 합니다 **최소** 로그 기록 될 수준입니다.</span><span class="sxs-lookup"><span data-stu-id="fdad5-234">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="fdad5-235">이 수준에서 기록 된 메시지 **수준을 테이블의 뒤에 나열 된 또는**에 기록 됩니다.</span><span class="sxs-lookup"><span data-stu-id="fdad5-235">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="fdad5-236">string</span><span class="sxs-lookup"><span data-stu-id="fdad5-236">string</span></span> | <span data-ttu-id="fdad5-237">LogLevel</span><span class="sxs-lookup"><span data-stu-id="fdad5-237">LogLevel</span></span> |
| - | - |
| `"trace"` | `LogLevel.Trace` |
| `"debug"` | `LogLevel.Debug` |
| <span data-ttu-id="fdad5-238">`"info"` **or** `"information"`</span><span class="sxs-lookup"><span data-stu-id="fdad5-238">`"info"` **or** `"information"`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="fdad5-239">`"warn"` **or** `"warning"`</span><span class="sxs-lookup"><span data-stu-id="fdad5-239">`"warn"` **or** `"warning"`</span></span> | `LogLevel.Warning` |
| `"error"` | `LogLevel.Error` |
| `"critical"` | `LogLevel.Critical` |
| `"none"` | `LogLevel.None` |

::: moniker-end

> [!NOTE]
> <span data-ttu-id="fdad5-240">로깅을 완전히 비활성화시키려면 `configureLogging` 메서드에서 `signalR.LogLevel.None`을 지정하십시오.</span><span class="sxs-lookup"><span data-stu-id="fdad5-240">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="fdad5-241">로깅에 대 한 자세한 내용은 참조는 [SignalR 진단 설명서](xref:signalr/diagnostics)합니다.</span><span class="sxs-lookup"><span data-stu-id="fdad5-241">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="fdad5-242">SignalR Java 클라이언트를 사용 하 여 [SLF4J](https://www.slf4j.org/) 로깅에 대 한 라이브러리입니다.</span><span class="sxs-lookup"><span data-stu-id="fdad5-242">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="fdad5-243">라이브러리의 사용자가 자신의 특정 로깅 구현을 특정 로깅 종속성에서 전환 하 여 선택한 수 있는 높은 수준의 로깅 API입니다.</span><span class="sxs-lookup"><span data-stu-id="fdad5-243">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="fdad5-244">다음 코드 조각을 사용 하는 방법을 보여 줍니다 `java.util.logging` SignalR Java 클라이언트를 사용 하 여 합니다.</span><span class="sxs-lookup"><span data-stu-id="fdad5-244">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="fdad5-245">종속성에 대 한 로깅을 구성 하지 않으면, SLF4J 다음 경고 메시지를 사용 하 여 기본 작업 없음로 거를 로드 합니다.</span><span class="sxs-lookup"><span data-stu-id="fdad5-245">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="fdad5-246">이 안전 하 게 무시할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fdad5-246">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="fdad5-247">허용되는 전송 구성</span><span class="sxs-lookup"><span data-stu-id="fdad5-247">Configure allowed transports</span></span>

<span data-ttu-id="fdad5-248">SignalR에서 사용되는 전송은 `WithUrl` 호출에서 (JavaScript에서는 `withUrl` 호출에서) 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fdad5-248">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="fdad5-249">`HttpTransportType` 값들의 비트 OR을 이용해서 클라이언트가 지정한 전송만 사용하도록 제한할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fdad5-249">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="fdad5-250">기본적으로 모든 전송은 활성화되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fdad5-250">All transports are enabled by default.</span></span>

<span data-ttu-id="fdad5-251">예를 들어 다음 코드는 서버-전송 이벤트 전송을 비활성화시키고 WebSocket 및 롱 폴링 연결만 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="fdad5-251">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="fdad5-252">JavaScript 클라이언트에서 전송은 `withUrl`에 제공되는 옵션 개체의 `transport` 필드를 설정하여 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="fdad5-252">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="fdad5-253">이 버전의 Java 클라이언트 websocket은만 사용할 수 있는 전송 합니다.</span><span class="sxs-lookup"><span data-stu-id="fdad5-253">In this version of the Java client websockets is the only available transport.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-3.0"

<span data-ttu-id="fdad5-254">Java 클라이언트에서 전송 선택 되어 있는 경우는 `withTransport` 메서드는 `HttpHubConnectionBuilder`합니다.</span><span class="sxs-lookup"><span data-stu-id="fdad5-254">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="fdad5-255">Java 클라이언트 Websocket 전송을 사용 하 여 기본값으로 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="fdad5-255">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="fdad5-256">SignalR Java 클라이언트는 아직 전송 대체 (fallback)를 지원 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="fdad5-256">The SignalR Java client doesn't support transport fallback yet.</span></span>

::: moniker-end

### <a name="configure-bearer-authentication"></a><span data-ttu-id="fdad5-257">전달자 인증 구성</span><span class="sxs-lookup"><span data-stu-id="fdad5-257">Configure bearer authentication</span></span>

<span data-ttu-id="fdad5-258">SignalR 요청과 함께 인증 데이터를 제공하려면 `AccessTokenProvider` 옵션(JavaScript에서는 `accessTokenFactory`를)을 이용해서 원하는 액세스 토큰을 반환하는 함수를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="fdad5-258">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="fdad5-259">.NET 클라이언트에서는 액세스 토큰이 HTTP "Bearer Authentication" 토큰으로 전달됩니다(`Bearer` 형식의 `Authorization` 헤더를 통해).</span><span class="sxs-lookup"><span data-stu-id="fdad5-259">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="fdad5-260">JavaScript 클라이언트에서는 브라우저의 API가 헤더 적용 기능을 제한하는 몇 가지 경우를 **제외**하면 (특히 서버-전송 이벤트 및 WebSockets 요청) 액세스 토큰이 전달자 토큰으로 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="fdad5-260">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="fdad5-261">이런 경우 액세스 토큰은 `access_token`이라는 쿼리 문자열 값으로 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="fdad5-261">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="fdad5-262">.NET 클라이언트에서 `AccessTokenProvider` 옵션은 `WithUrl`에서 옵션 대리자를 이용해서 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fdad5-262">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="fdad5-263">JavaScript 클라이언트에서는 `withUrl`에서 옵션 개체의 `accessTokenFactory` 필드를 설정하여 액세스 토큰을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="fdad5-263">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="fdad5-264">SignalR Java 클라이언트에서 전달자 토큰에 대 한 액세스 토큰 팩터리를 제공 하 여 인증에 사용 하도록 구성할 수 있습니다 합니다 [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)합니다.</span><span class="sxs-lookup"><span data-stu-id="fdad5-264">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="fdad5-265">사용 하 여 [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) 제공 하는 [RxJava](https://github.com/ReactiveX/RxJava) [Single\<문자열 >](https://reactivex.io/documentation/single.html)합니다.</span><span class="sxs-lookup"><span data-stu-id="fdad5-265">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="fdad5-266">호출 하 여 [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)를 클라이언트에 대 한 액세스 토큰을 생성 하는 논리를 작성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fdad5-266">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="fdad5-267">시간 제한 및 연결 유지 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="fdad5-267">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="fdad5-268">시간 제한 및 연결 유지 동작을 구성하기 위한 추가 옵션은 `HubConnection` 개체 자체에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fdad5-268">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="nettabdotnet"></a>[<span data-ttu-id="fdad5-269">.NET</span><span class="sxs-lookup"><span data-stu-id="fdad5-269">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="fdad5-270">옵션</span><span class="sxs-lookup"><span data-stu-id="fdad5-270">Option</span></span> | <span data-ttu-id="fdad5-271">기본값</span><span class="sxs-lookup"><span data-stu-id="fdad5-271">Default value</span></span> | <span data-ttu-id="fdad5-272">Description</span><span class="sxs-lookup"><span data-stu-id="fdad5-272">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="fdad5-273">30초(30000밀리초)</span><span class="sxs-lookup"><span data-stu-id="fdad5-273">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="fdad5-274">서버 활동 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="fdad5-274">Timeout for server activity.</span></span> <span data-ttu-id="fdad5-275">서버가 이 시간 제한 내에 메시지를 전송하지 않으면 클라이언트는 서버 연결이 끊어졌다고 간주하고 `Closed` 이벤트(JavaScript에서는 `onclose` 이벤트)를  트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="fdad5-275">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="fdad5-276">이 값이 충분히 커야만 ping 메시지가 서버에서 전송**되고** 시간 제한 내에 클라이언트에 의해 수신될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fdad5-276">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="fdad5-277">권장되는 값은 ping이 도착할 시간을 확보하기 위해 최소한 서버의 `KeepAliveInterval` 값의 두 배가 되는 숫자여야 합니다.</span><span class="sxs-lookup"><span data-stu-id="fdad5-277">The recommended value is a number at least double the server's `KeepAliveInterval` value, to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="fdad5-278">15초</span><span class="sxs-lookup"><span data-stu-id="fdad5-278">15 seconds</span></span> | <span data-ttu-id="fdad5-279">초기 서버 핸드셰이크에 대한 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="fdad5-279">Timeout for initial server handshake.</span></span> <span data-ttu-id="fdad5-280">서버가 이 시간 제한 내에 핸드셰이크 응답을 전송하지 않으면 클라이언트는 핸드셰이크를 취소하고 `Closed` 이벤트(JavaScript에서는 `onclose` 이벤트)를 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="fdad5-280">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="fdad5-281">이 설정은 심각한 네트워크 지연으로 인해 핸드셰이크 시간 제한 오류가 발생하는 경우에만 수정해야 하는 고급 설정입니다.</span><span class="sxs-lookup"><span data-stu-id="fdad5-281">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="fdad5-282">핸드셰이크 프로세스에 대한 자세한 내용은 [SignalR 허브 프로토콜 사양](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)을 참고하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="fdad5-282">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

<span data-ttu-id="fdad5-283">.NET 클라이언트에서 시간 제한 값은 `TimeSpan` 값으로 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="fdad5-283">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascripttabjavascript"></a>[<span data-ttu-id="fdad5-284">JavaScript</span><span class="sxs-lookup"><span data-stu-id="fdad5-284">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="fdad5-285">옵션</span><span class="sxs-lookup"><span data-stu-id="fdad5-285">Option</span></span> | <span data-ttu-id="fdad5-286">기본값</span><span class="sxs-lookup"><span data-stu-id="fdad5-286">Default value</span></span> | <span data-ttu-id="fdad5-287">Description</span><span class="sxs-lookup"><span data-stu-id="fdad5-287">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="fdad5-288">30초(30000밀리초)</span><span class="sxs-lookup"><span data-stu-id="fdad5-288">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="fdad5-289">서버 활동 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="fdad5-289">Timeout for server activity.</span></span> <span data-ttu-id="fdad5-290">클라이언트 연결이 끊어진 서버 및 트리거 고려 서버는이 간격에 메시지를 전송 되지 않은, 경우를 `onclose` 이벤트입니다.</span><span class="sxs-lookup"><span data-stu-id="fdad5-290">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="fdad5-291">이 값이 충분히 커야만 ping 메시지가 서버에서 전송**되고** 시간 제한 내에 클라이언트에 의해 수신될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fdad5-291">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="fdad5-292">권장되는 값은 ping이 도착할 시간을 확보하기 위해 최소한 서버의 `KeepAliveInterval` 값의 두 배가 되는 숫자여야 합니다.</span><span class="sxs-lookup"><span data-stu-id="fdad5-292">The recommended value is a number at least double the server's `KeepAliveInterval` value, to allow time for pings to arrive.</span></span> |

# <a name="javatabjava"></a>[<span data-ttu-id="fdad5-293">Java</span><span class="sxs-lookup"><span data-stu-id="fdad5-293">Java</span></span>](#tab/java)

| <span data-ttu-id="fdad5-294">옵션</span><span class="sxs-lookup"><span data-stu-id="fdad5-294">Option</span></span> | <span data-ttu-id="fdad5-295">기본값</span><span class="sxs-lookup"><span data-stu-id="fdad5-295">Default value</span></span> | <span data-ttu-id="fdad5-296">Description</span><span class="sxs-lookup"><span data-stu-id="fdad5-296">Description</span></span> |
| ----------- | ------------- | ----------- |
|<span data-ttu-id="fdad5-297">`getServerTimeout` `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="fdad5-297">`getServerTimeout` `setServerTimeout`</span></span> | <span data-ttu-id="fdad5-298">30초(30000밀리초)</span><span class="sxs-lookup"><span data-stu-id="fdad5-298">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="fdad5-299">서버 활동 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="fdad5-299">Timeout for server activity.</span></span> <span data-ttu-id="fdad5-300">클라이언트 연결이 끊어진 서버 및 트리거 고려 서버는이 간격에 메시지를 전송 되지 않은, 경우를 `onClose` 이벤트입니다.</span><span class="sxs-lookup"><span data-stu-id="fdad5-300">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="fdad5-301">이 값이 충분히 커야만 ping 메시지가 서버에서 전송**되고** 시간 제한 내에 클라이언트에 의해 수신될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fdad5-301">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="fdad5-302">권장되는 값은 ping이 도착할 시간을 확보하기 위해 최소한 서버의 `KeepAliveInterval` 값의 두 배가 되는 숫자여야 합니다.</span><span class="sxs-lookup"><span data-stu-id="fdad5-302">The recommended value is a number at least double the server's `KeepAliveInterval` value, to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="fdad5-303">15초</span><span class="sxs-lookup"><span data-stu-id="fdad5-303">15 seconds</span></span> | <span data-ttu-id="fdad5-304">초기 서버 핸드셰이크에 대한 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="fdad5-304">Timeout for initial server handshake.</span></span> <span data-ttu-id="fdad5-305">서버는이 간격의 핸드셰이크 응답을 보내지 않습니다, 클라이언트 취소 핸드셰이크 및 트리거는 `onClose` 이벤트입니다.</span><span class="sxs-lookup"><span data-stu-id="fdad5-305">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="fdad5-306">이 설정은 심각한 네트워크 지연으로 인해 핸드셰이크 시간 제한 오류가 발생하는 경우에만 수정해야 하는 고급 설정입니다.</span><span class="sxs-lookup"><span data-stu-id="fdad5-306">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="fdad5-307">핸드셰이크 프로세스에 대한 자세한 내용은 [SignalR 허브 프로토콜 사양](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)을 참고하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="fdad5-307">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="fdad5-308">추가 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="fdad5-308">Configure additional options</span></span>

<span data-ttu-id="fdad5-309">추가 옵션에서 구성할 수 있습니다는 `WithUrl` (`withUrl` javascript에서) 메서드 `HubConnectionBuilder` 또는 다양 한 구성 Api에는 `HttpHubConnectionBuilder` Java 클라이언트에서:</span><span class="sxs-lookup"><span data-stu-id="fdad5-309">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="nettabdotnet"></a>[<span data-ttu-id="fdad5-310">.NET</span><span class="sxs-lookup"><span data-stu-id="fdad5-310">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="fdad5-311">.NET 옵션</span><span class="sxs-lookup"><span data-stu-id="fdad5-311">.NET Option</span></span> |  <span data-ttu-id="fdad5-312">기본값</span><span class="sxs-lookup"><span data-stu-id="fdad5-312">Default value</span></span> | <span data-ttu-id="fdad5-313">Description</span><span class="sxs-lookup"><span data-stu-id="fdad5-313">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="fdad5-314">HTTP 요청에서 전달자 인증 토큰으로 제공된 문자열을 반환하는 함수입니다.</span><span class="sxs-lookup"><span data-stu-id="fdad5-314">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="fdad5-315">이 값을 `true`로 설정하면 협상 단계를 건너뜁니다.</span><span class="sxs-lookup"><span data-stu-id="fdad5-315">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="fdad5-316">**WebSockets 전송이 유일하게 활성화된 전송인 경우에만 지원됩니다.**</span><span class="sxs-lookup"><span data-stu-id="fdad5-316">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="fdad5-317">Azure SignalR Service를 사용하는 경우에는 이 설정을 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="fdad5-317">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="fdad5-318">Empty</span><span class="sxs-lookup"><span data-stu-id="fdad5-318">Empty</span></span> | <span data-ttu-id="fdad5-319">요청을 인증하기 위해 전송할 TLS 인증서의 컬렉션입니다.</span><span class="sxs-lookup"><span data-stu-id="fdad5-319">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="fdad5-320">Empty</span><span class="sxs-lookup"><span data-stu-id="fdad5-320">Empty</span></span> | <span data-ttu-id="fdad5-321">모든 HTTP 요청과 함께 전송할 HTTP 쿠키의 컬렉션입니다.</span><span class="sxs-lookup"><span data-stu-id="fdad5-321">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="fdad5-322">Empty</span><span class="sxs-lookup"><span data-stu-id="fdad5-322">Empty</span></span> | <span data-ttu-id="fdad5-323">모든 HTTP 요청과 함께 전송할 자격 증명입니다.</span><span class="sxs-lookup"><span data-stu-id="fdad5-323">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="fdad5-324">5초</span><span class="sxs-lookup"><span data-stu-id="fdad5-324">5 seconds</span></span> | <span data-ttu-id="fdad5-325">Websocket에만 해당됩니다.</span><span class="sxs-lookup"><span data-stu-id="fdad5-325">WebSockets only.</span></span> <span data-ttu-id="fdad5-326">클라이언트가 서버를 닫은 후 닫기 요청을 확인하기 위해 대기하는 최대 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="fdad5-326">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="fdad5-327">서버가 이 시간 내에 종료를 승인하지 않으면 클라이언트가 연결을 끊습니다.</span><span class="sxs-lookup"><span data-stu-id="fdad5-327">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="fdad5-328">Empty</span><span class="sxs-lookup"><span data-stu-id="fdad5-328">Empty</span></span> | <span data-ttu-id="fdad5-329">추가 HTTP 헤더의 모든 HTTP 요청과 함께 보낼 맵.</span><span class="sxs-lookup"><span data-stu-id="fdad5-329">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="fdad5-330">HTTP 요청을 전송하기 위해 사용되는 `HttpMessageHandler`를 구성하거나 대체하는 데 사용할 수 있는 대리자입니다.</span><span class="sxs-lookup"><span data-stu-id="fdad5-330">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="fdad5-331">WebSocket 연결에는 사용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="fdad5-331">Not used for WebSocket connections.</span></span> <span data-ttu-id="fdad5-332">이 대리자는 null이 아닌 값을 반환해야 하며 매개 변수로 기본값을 받습니다.</span><span class="sxs-lookup"><span data-stu-id="fdad5-332">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="fdad5-333">이 기본값의 설정을 수정하여 반환하거나 새로운 `HttpMessageHandler` 인스턴스를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="fdad5-333">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="fdad5-334">**그렇지 않은 경우 처리기를 교체 해야 제공된 된 처리기에서 유지 하려는 설정을 복사, 구성된 옵션 (예: 쿠키 및 헤더) 새 처리기에 적용 되지 않습니다.**</span><span class="sxs-lookup"><span data-stu-id="fdad5-334">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="fdad5-335">HTTP 요청을 전송할 때 사용할 HTTP 프록시입니다.</span><span class="sxs-lookup"><span data-stu-id="fdad5-335">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="fdad5-336">HTTP 및 WebSockets 요청에 대한 기본 자격 증명을 전송하려면 이 부울 값을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="fdad5-336">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="fdad5-337">그러면 Windows 인증을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fdad5-337">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="fdad5-338">추가적인 WebSocket 옵션을 구성할 수 있는 대리자입니다.</span><span class="sxs-lookup"><span data-stu-id="fdad5-338">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="fdad5-339">옵션을 구성에 사용할 수 있는 [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions)의 인스턴스를 전달받습니다.</span><span class="sxs-lookup"><span data-stu-id="fdad5-339">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascripttabjavascript"></a>[<span data-ttu-id="fdad5-340">JavaScript</span><span class="sxs-lookup"><span data-stu-id="fdad5-340">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="fdad5-341">JavaScript 옵션</span><span class="sxs-lookup"><span data-stu-id="fdad5-341">JavaScript Option</span></span> | <span data-ttu-id="fdad5-342">기본값</span><span class="sxs-lookup"><span data-stu-id="fdad5-342">Default Value</span></span> | <span data-ttu-id="fdad5-343">설명</span><span class="sxs-lookup"><span data-stu-id="fdad5-343">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="fdad5-344">HTTP 요청에서 전달자 인증 토큰으로 제공된 문자열을 반환하는 함수입니다.</span><span class="sxs-lookup"><span data-stu-id="fdad5-344">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="fdad5-345">이 값을 `true`로 설정하면 협상 단계를 건너뜁니다.</span><span class="sxs-lookup"><span data-stu-id="fdad5-345">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="fdad5-346">**WebSockets 전송이 유일하게 활성화된 전송인 경우에만 지원됩니다.**</span><span class="sxs-lookup"><span data-stu-id="fdad5-346">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="fdad5-347">Azure SignalR Service를 사용하는 경우에는 이 설정을 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="fdad5-347">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="javatabjava"></a>[<span data-ttu-id="fdad5-348">Java</span><span class="sxs-lookup"><span data-stu-id="fdad5-348">Java</span></span>](#tab/java)

| <span data-ttu-id="fdad5-349">Java 옵션</span><span class="sxs-lookup"><span data-stu-id="fdad5-349">Java Option</span></span> | <span data-ttu-id="fdad5-350">기본값</span><span class="sxs-lookup"><span data-stu-id="fdad5-350">Default Value</span></span> | <span data-ttu-id="fdad5-351">설명</span><span class="sxs-lookup"><span data-stu-id="fdad5-351">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="fdad5-352">HTTP 요청에서 전달자 인증 토큰으로 제공된 문자열을 반환하는 함수입니다.</span><span class="sxs-lookup"><span data-stu-id="fdad5-352">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="fdad5-353">이 값을 `true`로 설정하면 협상 단계를 건너뜁니다.</span><span class="sxs-lookup"><span data-stu-id="fdad5-353">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="fdad5-354">**WebSockets 전송이 유일하게 활성화된 전송인 경우에만 지원됩니다.**</span><span class="sxs-lookup"><span data-stu-id="fdad5-354">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="fdad5-355">Azure SignalR Service를 사용하는 경우에는 이 설정을 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="fdad5-355">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="fdad5-356">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="fdad5-356">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="fdad5-357">Empty</span><span class="sxs-lookup"><span data-stu-id="fdad5-357">Empty</span></span> | <span data-ttu-id="fdad5-358">추가 HTTP 헤더의 모든 HTTP 요청과 함께 보낼 맵.</span><span class="sxs-lookup"><span data-stu-id="fdad5-358">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="fdad5-359">.NET 클라이언트에서 이 옵션들은 `WithUrl`에 제공되는 옵션 대리자를 이용해서 수정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fdad5-359">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="fdad5-360">JavaScript 클라이언트에서 이 옵션들은 `withUrl`에 제공되는 JavaScript 개체를 통해서 제공될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fdad5-360">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="fdad5-361">Java 클라이언트에서 이러한 옵션에서 구성할 수 있습니다 메서드를 사용 하 여는 `HttpHubConnectionBuilder` 에서 반환 되는 `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="fdad5-361">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="fdad5-362">추가 자료</span><span class="sxs-lookup"><span data-stu-id="fdad5-362">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>
