---
title: ASP.NET Core SignalR 구성
author: bradygaster
description: ASP.NET Core SignalR 앱을 구성하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 08/05/2019
uid: signalr/configuration
ms.openlocfilehash: 475d9664c588c06bfcd816959be8a425ee01c023
ms.sourcegitcommit: 776367717e990bdd600cb3c9148ffb905d56862d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/09/2019
ms.locfileid: "68915077"
---
# <a name="aspnet-core-signalr-configuration"></a><span data-ttu-id="9cac7-103">ASP.NET Core SignalR 구성</span><span class="sxs-lookup"><span data-stu-id="9cac7-103">ASP.NET Core SignalR configuration</span></span>

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="9cac7-104">JSON/MessagePack serialization 옵션</span><span class="sxs-lookup"><span data-stu-id="9cac7-104">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="9cac7-105">ASP.NET Core SignalR는 메시지 인코딩에 대 한 두 가지 프로토콜을 지원 합니다. [JSON](https://www.json.org/) 및 [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="9cac7-105">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="9cac7-106">각 프로토콜에는 serialization 구성 옵션이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-106">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="9cac7-107">JSON serialization을 사용 하 여 서버에 구성할 수 있습니다 합니다 [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) 후 추가할 수 있는 확장 메서드 [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) 에서 프로그램 `Startup.ConfigureServices` 메서드.</span><span class="sxs-lookup"><span data-stu-id="9cac7-107">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="9cac7-108">`AddJsonProtocol` 메서드는 `options` 개체를 전달받는 대리자를 받습니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-108">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="9cac7-109">이 개체의 [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) 속성은 JSON.NET의 `JsonSerializerSettings` 개체로, 인수 및 반환 값의 직렬화를 구성하는 데 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-109">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="9cac7-110">더 자세한 내용은 [JSON.NET 설명서](https://www.newtonsoft.com/json/help/html/Introduction.htm)를 참고하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-110">See the [JSON.NET Documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm) for more details.</span></span>

<span data-ttu-id="9cac7-111">예를 들어 기본 값인 "camelCase" 속성 이름 대신 "PascalCase" 속성 이름을 사용하도록 직렬화 변환기를 구성하려면 다음 코드를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-111">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
        new DefaultContractResolver();
    });
```

<span data-ttu-id="9cac7-112">.NET 클라이언트에서는 [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)에 동일한 `AddJsonProtocol` 확장 메서드가 존재합니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-112">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="9cac7-113">이 확장 메서드를 확인하려면 `Microsoft.Extensions.DependencyInjection` 네임스페이스를 임포트해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-113">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="9cac7-114">현재 JavaScript 클라이언트에서는 JSON 직렬화를 구성할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-114">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="9cac7-115">MessagePack serialization 옵션</span><span class="sxs-lookup"><span data-stu-id="9cac7-115">MessagePack serialization options</span></span>

<span data-ttu-id="9cac7-116">MessagePack 직렬화는 [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) 호출에 대리자를 제공하여 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-116">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="9cac7-117">더 자세한 내용은 [SignalR의 MessagePack](xref:signalr/messagepackhubprotocol)을 참고하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-117">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="9cac7-118">현재 JavaScript 클라이언트에서는 MessagePack 직렬화를 구성할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-118">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="9cac7-119">서버 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="9cac7-119">Configure server options</span></span>

<span data-ttu-id="9cac7-120">다음 표는 SignalR 허브를 구성하기 위한 옵션을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-120">The following table describes options for configuring SignalR hubs:</span></span>

::: moniker range=">= aspnetcore-3.0"

| <span data-ttu-id="9cac7-121">옵션</span><span class="sxs-lookup"><span data-stu-id="9cac7-121">Option</span></span> | <span data-ttu-id="9cac7-122">기본값</span><span class="sxs-lookup"><span data-stu-id="9cac7-122">Default Value</span></span> | <span data-ttu-id="9cac7-123">설명</span><span class="sxs-lookup"><span data-stu-id="9cac7-123">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="9cac7-124">30초</span><span class="sxs-lookup"><span data-stu-id="9cac7-124">30 seconds</span></span> | <span data-ttu-id="9cac7-125">서버는이 간격 내에 메시지를 받지 못한 경우 (keep-alive 포함) 클라이언트의 연결을 해제 하는 것으로 간주 합니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-125">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="9cac7-126">이를 구현 하는 방법으로 인해 클라이언트에서 실제로 연결이 끊어진 것으로 표시 되는 시간 제한 간격 보다 오래 걸릴 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-126">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="9cac7-127">권장 값은 `KeepAliveInterval` 값 두 배가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-127">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="9cac7-128">15초</span><span class="sxs-lookup"><span data-stu-id="9cac7-128">15 seconds</span></span> | <span data-ttu-id="9cac7-129">클라이언트가 이 시간 제한 내에 초기 핸드셰이크 메시지를 전송하지 않으면 연결이 닫힙니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-129">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="9cac7-130">이 설정은 심각한 네트워크 지연으로 인해 핸드셰이크 시간 제한 오류가 발생하는 경우에만 수정해야 하는 고급 설정입니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-130">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="9cac7-131">핸드셰이크 프로세스에 대한 보다 자세한 내용은 [SignalR 허브 프로토콜 사양](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)을 참고하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-131">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="9cac7-132">15초</span><span class="sxs-lookup"><span data-stu-id="9cac7-132">15 seconds</span></span> | <span data-ttu-id="9cac7-133">서버가 이 간격 내에 메시지를 전송하지 않으면 자동으로 ping 메시지가 전송되어 연결이 열린 상태로 유지됩니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-133">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="9cac7-134">`KeepAliveInterval`을 변경할 경우 클라이언트의 `ServerTimeout`/`serverTimeoutInMilliseconds` 설정도 변경하십시오.</span><span class="sxs-lookup"><span data-stu-id="9cac7-134">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="9cac7-135">권장되는 `ServerTimeout`/`serverTimeoutInMilliseconds` 값은 `KeepAliveInterval` 값의 두 배입니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-135">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="9cac7-136">설치된 모든 프로토콜</span><span class="sxs-lookup"><span data-stu-id="9cac7-136">All installed protocols</span></span> | <span data-ttu-id="9cac7-137">허브가 지원하는 프로토콜입니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-137">Protocols supported by this hub.</span></span> <span data-ttu-id="9cac7-138">기본적으로 서버에 등록된 모든 프로토콜이 허용되지만 이 목록에서 프로토콜을 제거하여 개별 허브에 대해 특정 프로토콜을 비활성화시킬 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-138">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="9cac7-139">이 값이 `true`면 Hub 메서드에서 예외가 발생할 경우 자세한 예외 메시지가 클라이언트로 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-139">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="9cac7-140">예외 메시지에는 민감한 정보가 포함되어 있을 수 있으므로 기본값은 `false`입니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-140">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="9cac7-141">클라이언트 업로드 스트림에 대해 버퍼링 할 수 있는 최대 항목 수입니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-141">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="9cac7-142">이 제한에 도달 하면 서버에서 스트림 항목을 처리할 때까지 호출 처리가 차단 됩니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-142">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="9cac7-143">32 KB</span><span class="sxs-lookup"><span data-stu-id="9cac7-143">32 KB</span></span> | <span data-ttu-id="9cac7-144">들어오는 단일 허브 메시지의 최대 크기입니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-144">Maximum size of a single incoming hub message.</span></span> |

::: moniker-end

::: moniker range="= aspnetcore-2.2"

| <span data-ttu-id="9cac7-145">옵션</span><span class="sxs-lookup"><span data-stu-id="9cac7-145">Option</span></span> | <span data-ttu-id="9cac7-146">기본값</span><span class="sxs-lookup"><span data-stu-id="9cac7-146">Default Value</span></span> | <span data-ttu-id="9cac7-147">Description</span><span class="sxs-lookup"><span data-stu-id="9cac7-147">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="9cac7-148">30초</span><span class="sxs-lookup"><span data-stu-id="9cac7-148">30 seconds</span></span> | <span data-ttu-id="9cac7-149">서버는이 간격 내에 메시지를 받지 못한 경우 (keep-alive 포함) 클라이언트의 연결을 해제 하는 것으로 간주 합니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-149">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="9cac7-150">이를 구현 하는 방법으로 인해 클라이언트에서 실제로 연결이 끊어진 것으로 표시 되는 시간 제한 간격 보다 오래 걸릴 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-150">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="9cac7-151">권장 값은 `KeepAliveInterval` 값 두 배가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-151">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="9cac7-152">15초</span><span class="sxs-lookup"><span data-stu-id="9cac7-152">15 seconds</span></span> | <span data-ttu-id="9cac7-153">클라이언트가 이 시간 제한 내에 초기 핸드셰이크 메시지를 전송하지 않으면 연결이 닫힙니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-153">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="9cac7-154">이 설정은 심각한 네트워크 지연으로 인해 핸드셰이크 시간 제한 오류가 발생하는 경우에만 수정해야 하는 고급 설정입니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-154">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="9cac7-155">핸드셰이크 프로세스에 대한 보다 자세한 내용은 [SignalR 허브 프로토콜 사양](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)을 참고하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-155">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="9cac7-156">15초</span><span class="sxs-lookup"><span data-stu-id="9cac7-156">15 seconds</span></span> | <span data-ttu-id="9cac7-157">서버가 이 간격 내에 메시지를 전송하지 않으면 자동으로 ping 메시지가 전송되어 연결이 열린 상태로 유지됩니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-157">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="9cac7-158">`KeepAliveInterval`을 변경할 경우 클라이언트의 `ServerTimeout`/`serverTimeoutInMilliseconds` 설정도 변경하십시오.</span><span class="sxs-lookup"><span data-stu-id="9cac7-158">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="9cac7-159">권장되는 `ServerTimeout`/`serverTimeoutInMilliseconds` 값은 `KeepAliveInterval` 값의 두 배입니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-159">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="9cac7-160">설치된 모든 프로토콜</span><span class="sxs-lookup"><span data-stu-id="9cac7-160">All installed protocols</span></span> | <span data-ttu-id="9cac7-161">허브가 지원하는 프로토콜입니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-161">Protocols supported by this hub.</span></span> <span data-ttu-id="9cac7-162">기본적으로 서버에 등록된 모든 프로토콜이 허용되지만 이 목록에서 프로토콜을 제거하여 개별 허브에 대해 특정 프로토콜을 비활성화시킬 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-162">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="9cac7-163">이 값이 `true`면 Hub 메서드에서 예외가 발생할 경우 자세한 예외 메시지가 클라이언트로 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-163">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="9cac7-164">예외 메시지에는 민감한 정보가 포함되어 있을 수 있으므로 기본값은 `false`입니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-164">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

::: moniker-end

::: moniker range="= aspnetcore-2.1"

| <span data-ttu-id="9cac7-165">옵션</span><span class="sxs-lookup"><span data-stu-id="9cac7-165">Option</span></span> | <span data-ttu-id="9cac7-166">기본값</span><span class="sxs-lookup"><span data-stu-id="9cac7-166">Default Value</span></span> | <span data-ttu-id="9cac7-167">설명</span><span class="sxs-lookup"><span data-stu-id="9cac7-167">Description</span></span> |
| ------ | ------------- | ----------- |
| `HandshakeTimeout` | <span data-ttu-id="9cac7-168">15초</span><span class="sxs-lookup"><span data-stu-id="9cac7-168">15 seconds</span></span> | <span data-ttu-id="9cac7-169">클라이언트가 이 시간 제한 내에 초기 핸드셰이크 메시지를 전송하지 않으면 연결이 닫힙니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-169">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="9cac7-170">이 설정은 심각한 네트워크 지연으로 인해 핸드셰이크 시간 제한 오류가 발생하는 경우에만 수정해야 하는 고급 설정입니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-170">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="9cac7-171">핸드셰이크 프로세스에 대한 보다 자세한 내용은 [SignalR 허브 프로토콜 사양](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)을 참고하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-171">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="9cac7-172">15초</span><span class="sxs-lookup"><span data-stu-id="9cac7-172">15 seconds</span></span> | <span data-ttu-id="9cac7-173">서버가 이 간격 내에 메시지를 전송하지 않으면 자동으로 ping 메시지가 전송되어 연결이 열린 상태로 유지됩니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-173">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="9cac7-174">`KeepAliveInterval`을 변경할 경우 클라이언트의 `ServerTimeout`/`serverTimeoutInMilliseconds` 설정도 변경하십시오.</span><span class="sxs-lookup"><span data-stu-id="9cac7-174">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="9cac7-175">권장되는 `ServerTimeout`/`serverTimeoutInMilliseconds` 값은 `KeepAliveInterval` 값의 두 배입니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-175">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="9cac7-176">설치된 모든 프로토콜</span><span class="sxs-lookup"><span data-stu-id="9cac7-176">All installed protocols</span></span> | <span data-ttu-id="9cac7-177">허브가 지원하는 프로토콜입니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-177">Protocols supported by this hub.</span></span> <span data-ttu-id="9cac7-178">기본적으로 서버에 등록된 모든 프로토콜이 허용되지만 이 목록에서 프로토콜을 제거하여 개별 허브에 대해 특정 프로토콜을 비활성화시킬 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-178">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="9cac7-179">이 값이 `true`면 Hub 메서드에서 예외가 발생할 경우 자세한 예외 메시지가 클라이언트로 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-179">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="9cac7-180">예외 메시지에는 민감한 정보가 포함되어 있을 수 있으므로 기본값은 `false`입니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-180">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

::: moniker-end

<span data-ttu-id="9cac7-181">`Startup.ConfigureServices`에서 `AddSignalR` 호출에 옵션 대리자를 제공하여 모든 허브에 대한 옵션을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-181">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="9cac7-182">단일 허브에 대 한 옵션은에서 `AddSignalR` 제공 되는 전역 옵션을 재정의 하 고 다음을 사용 하 여 <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-182">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="9cac7-183">고급 HTTP 구성 옵션</span><span class="sxs-lookup"><span data-stu-id="9cac7-183">Advanced HTTP configuration options</span></span>

<span data-ttu-id="9cac7-184">전송 및 메모리 버퍼 관리와 관련된 고급 설정을 구성하려면 `HttpConnectionDispatcherOptions`를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-184">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="9cac7-185">이러한 옵션은의 `Startup.Configure` [maphub\<T >](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) 에 대리자를 전달 하 여 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-185">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="9cac7-186">다음 표에서는 ASP.NET Core SignalR의 고급 HTTP 옵션을 구성 하는 옵션을 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-186">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

::: moniker range=">= aspnetcore-3.0"

| <span data-ttu-id="9cac7-187">옵션</span><span class="sxs-lookup"><span data-stu-id="9cac7-187">Option</span></span> | <span data-ttu-id="9cac7-188">기본값</span><span class="sxs-lookup"><span data-stu-id="9cac7-188">Default Value</span></span> | <span data-ttu-id="9cac7-189">설명</span><span class="sxs-lookup"><span data-stu-id="9cac7-189">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="9cac7-190">32 KB</span><span class="sxs-lookup"><span data-stu-id="9cac7-190">32 KB</span></span> | <span data-ttu-id="9cac7-191">클라이언트에서 수신 된 백 압력을 적용 하기 전에 버퍼링 되는 최대 바이트 수입니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-191">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="9cac7-192">이 값을 늘리면 서버는 백 압력을 적용 하지 않고 더 큰 메시지를 더 빨리 수신할 수 있지만 메모리 사용을 높일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-192">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="9cac7-193">허브 클래스에 적용된 `Authorize` 특성에서 자동으로 수집된 데이터입니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-193">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="9cac7-194">클라이언트가 허브에 연결할 권한이 있는지 확인하는 데 사용되는 [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) 개체의 목록입니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-194">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="9cac7-195">32 KB</span><span class="sxs-lookup"><span data-stu-id="9cac7-195">32 KB</span></span> | <span data-ttu-id="9cac7-196">서버에서 백 압력을 관찰 하기 전에 버퍼링 하는 앱에서 보낸 최대 바이트 수입니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-196">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="9cac7-197">이 값을 늘리면 서버에서 백 압력을 대기 하지 않고 더 큰 메시지를 더 빨리 버퍼링 할 수 있지만 메모리 소비가 증가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-197">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="9cac7-198">모든 전송을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-198">All Transports are enabled.</span></span> | <span data-ttu-id="9cac7-199">클라이언트에서 연결 하는 `HttpTransportType` 데 사용할 수 있는 전송을 제한할 수 있는 값의 비트 플래그 열거형입니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-199">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="9cac7-200">아래를 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="9cac7-200">See below.</span></span> | <span data-ttu-id="9cac7-201">롱 폴링 전송과 관련된 추가 옵션입니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-201">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="9cac7-202">아래 내용을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="9cac7-202">See below.</span></span> | <span data-ttu-id="9cac7-203">WebSockets 전송과 관련된 추가 옵션입니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-203">Additional options specific to the WebSockets transport.</span></span> |

::: moniker-end

::: moniker range="< aspnetcore-3.0"

| <span data-ttu-id="9cac7-204">옵션</span><span class="sxs-lookup"><span data-stu-id="9cac7-204">Option</span></span> | <span data-ttu-id="9cac7-205">기본값</span><span class="sxs-lookup"><span data-stu-id="9cac7-205">Default Value</span></span> | <span data-ttu-id="9cac7-206">설명</span><span class="sxs-lookup"><span data-stu-id="9cac7-206">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="9cac7-207">32 KB</span><span class="sxs-lookup"><span data-stu-id="9cac7-207">32 KB</span></span> | <span data-ttu-id="9cac7-208">클라이언트로부터 수신하는 서버 버퍼의 최대 바이트 수입니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-208">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="9cac7-209">이 값을 늘리면 서버가 더 큰 메시지를 수신할 수 있지만 메모리 소비에 부정적인 영향을 줄 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-209">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="9cac7-210">허브 클래스에 적용된 `Authorize` 특성에서 자동으로 수집된 데이터입니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-210">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="9cac7-211">클라이언트가 허브에 연결할 권한이 있는지 확인하는 데 사용되는 [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) 개체의 목록입니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-211">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="9cac7-212">32 KB</span><span class="sxs-lookup"><span data-stu-id="9cac7-212">32 KB</span></span> | <span data-ttu-id="9cac7-213">앱에서 전송하는 서버 버퍼의 최대 바이트 수입니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-213">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="9cac7-214">이 값을 늘리면 서버가 더 큰 메시지를 전송할 수 있지만 메모리 소비에 부정적인 영향을 줄 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-214">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="9cac7-215">모든 전송을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-215">All Transports are enabled.</span></span> | <span data-ttu-id="9cac7-216">클라이언트에서 연결 하는 `HttpTransportType` 데 사용할 수 있는 전송을 제한할 수 있는 값의 비트 플래그 열거형입니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-216">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="9cac7-217">아래를 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="9cac7-217">See below.</span></span> | <span data-ttu-id="9cac7-218">롱 폴링 전송과 관련된 추가 옵션입니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-218">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="9cac7-219">아래 내용을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="9cac7-219">See below.</span></span> | <span data-ttu-id="9cac7-220">WebSockets 전송과 관련된 추가 옵션입니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-220">Additional options specific to the WebSockets transport.</span></span> |

::: moniker-end

<span data-ttu-id="9cac7-221">롱 폴링 전송에는 `LongPolling` 속성을 이용해서 구성할 수 있는 추가적인 옵션이 존재합니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-221">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="9cac7-222">옵션</span><span class="sxs-lookup"><span data-stu-id="9cac7-222">Option</span></span> | <span data-ttu-id="9cac7-223">기본값</span><span class="sxs-lookup"><span data-stu-id="9cac7-223">Default Value</span></span> | <span data-ttu-id="9cac7-224">설명</span><span class="sxs-lookup"><span data-stu-id="9cac7-224">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="9cac7-225">90초</span><span class="sxs-lookup"><span data-stu-id="9cac7-225">90 seconds</span></span> | <span data-ttu-id="9cac7-226">단일 폴링 요청을 종료하기 전에 서버가 메시지를 클라이언트에 전송할 때까지 대기하는 최대 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-226">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="9cac7-227">이 값을 줄이면 클라이언트는 더 자주 새 폴링 요청을 발행합니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-227">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="9cac7-228">WebSocket 전송에는 `WebSockets` 속성을 이용해서 구성할 수 있는 추가적인 옵션이 존재합니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-228">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="9cac7-229">옵션</span><span class="sxs-lookup"><span data-stu-id="9cac7-229">Option</span></span> | <span data-ttu-id="9cac7-230">기본값</span><span class="sxs-lookup"><span data-stu-id="9cac7-230">Default Value</span></span> | <span data-ttu-id="9cac7-231">설명</span><span class="sxs-lookup"><span data-stu-id="9cac7-231">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="9cac7-232">5초</span><span class="sxs-lookup"><span data-stu-id="9cac7-232">5 seconds</span></span> | <span data-ttu-id="9cac7-233">서버가 닫힌 후 클라이언트가 이 시간 제한 내에 닫기에 실패하면 연결이 종료됩니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-233">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="9cac7-234">사용자 지정 값으로 `Sec-WebSocket-Protocol` 헤더를 설정하기 위해 사용할 수 있는 대리자입니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-234">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="9cac7-235">이 대리자는 클라이언트에서 요청한 값을 입력으로 받아서 원하는 값을 반환해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-235">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="9cac7-236">클라이언트 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="9cac7-236">Configure client options</span></span>

<span data-ttu-id="9cac7-237">클라이언트 옵션은 `HubConnectionBuilder` 형식 (.net 및 JavaScript 클라이언트에서 사용 가능)에서 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-237">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="9cac7-238">Java 클라이언트 에서도 사용할 수 있지만 `HttpHubConnectionBuilder` 하위 클래스에는 작성기 구성 옵션 `HubConnection` 뿐만 아니라 자체도 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-238">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="9cac7-239">로깅 구성</span><span class="sxs-lookup"><span data-stu-id="9cac7-239">Configure logging</span></span>

<span data-ttu-id="9cac7-240">.NET 클라이언트에서 로깅은 `ConfigureLogging` 메서드를 이용해서 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-240">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="9cac7-241">로깅 공급자 및 필터는 서버와 동일한 방법으로 등록할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-241">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="9cac7-242">자세한 내용은 [ASP.NET Core 로깅](xref:fundamentals/logging/index) 설명서를 참고하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-242">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="9cac7-243">로깅 공급자를 등록하는 데 필요한 패키지를 설치해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-243">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="9cac7-244">전체 목록은 로깅 문서의 [기본 제공 로깅 공급자](xref:fundamentals/logging/index#built-in-logging-providers) 섹션을 참고하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-244">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="9cac7-245">예를 들어 콘솔 로깅을 사용하려면 `Microsoft.Extensions.Logging.Console` NuGet 패키지를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-245">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="9cac7-246">`AddConsole` 확장 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-246">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="9cac7-247">JavaScript 클라이언트에는 유사한 `configureLogging` 메서드가 존재합니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-247">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="9cac7-248">기록할 로그 메시지의 최소 수준을 나타내는 `LogLevel` 값을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-248">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="9cac7-249">로그는 브라우저 콘솔 창에 기록됩니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-249">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="9cac7-250">값 대신 로그 수준 이름을 나타내는 값을 `string` 제공할 수도 있습니다. `LogLevel`</span><span class="sxs-lookup"><span data-stu-id="9cac7-250">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="9cac7-251">이는 `LogLevel` 상수에 대 한 액세스 권한이 없는 환경에서 SignalR 로깅을 구성할 때 유용 합니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-251">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="9cac7-252">다음 표에서는 사용 가능한 로그 수준을 나열 합니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-252">The following table lists the available log levels.</span></span> <span data-ttu-id="9cac7-253">사용자가 지정 `configureLogging` 하는 값은 로깅할 **최소** 로그 수준을 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-253">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="9cac7-254">이 수준에서 기록 되 **는 메시지 또는 테이블의 이후 나열 된 수준**에 기록 됩니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-254">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="9cac7-255">문자열</span><span class="sxs-lookup"><span data-stu-id="9cac7-255">String</span></span>                      | <span data-ttu-id="9cac7-256">LogLevel</span><span class="sxs-lookup"><span data-stu-id="9cac7-256">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="9cac7-257">`info` **or** `information`</span><span class="sxs-lookup"><span data-stu-id="9cac7-257">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="9cac7-258">`warn` **or** `warning`</span><span class="sxs-lookup"><span data-stu-id="9cac7-258">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

::: moniker-end

> [!NOTE]
> <span data-ttu-id="9cac7-259">로깅을 완전히 비활성화시키려면 `configureLogging` 메서드에서 `signalR.LogLevel.None`을 지정하십시오.</span><span class="sxs-lookup"><span data-stu-id="9cac7-259">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="9cac7-260">로깅에 대 한 자세한 내용은 [SignalR 진단 설명서](xref:signalr/diagnostics)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="9cac7-260">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="9cac7-261">SignalR Java 클라이언트는 [SLF4J](https://www.slf4j.org/) 라이브러리를 사용 하 여 로깅을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-261">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="9cac7-262">이는 라이브러리의 사용자가 특정 로깅 종속성을 가져와 자신의 특정 로깅 구현을 선택할 수 있게 해 주는 상위 수준 로깅 API입니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-262">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="9cac7-263">다음 코드 조각에서는 SignalR Java 클라이언트와 `java.util.logging` 함께를 사용 하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-263">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="9cac7-264">종속성에서 로깅을 구성 하지 않으면 SLF4J는 다음과 같은 경고 메시지와 함께 기본 작업 없음로 거를 로드 합니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-264">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="9cac7-265">이는 무시 해도 됩니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-265">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="9cac7-266">허용되는 전송 구성</span><span class="sxs-lookup"><span data-stu-id="9cac7-266">Configure allowed transports</span></span>

<span data-ttu-id="9cac7-267">SignalR에서 사용되는 전송은 `WithUrl` 호출에서 (JavaScript에서는 `withUrl` 호출에서) 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-267">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="9cac7-268">`HttpTransportType` 값들의 비트 OR을 이용해서 클라이언트가 지정한 전송만 사용하도록 제한할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-268">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="9cac7-269">기본적으로 모든 전송은 활성화되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-269">All transports are enabled by default.</span></span>

<span data-ttu-id="9cac7-270">예를 들어 다음 코드는 서버-전송 이벤트 전송을 비활성화시키고 WebSocket 및 롱 폴링 연결만 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-270">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="9cac7-271">JavaScript 클라이언트에서 전송은 `withUrl`에 제공되는 옵션 개체의 `transport` 필드를 설정하여 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-271">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="9cac7-272">이 버전의 Java 클라이언트 websocket은 유일 하 게 사용할 수 있는 전송입니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-272">In this version of the Java client websockets is the only available transport.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-3.0"

<span data-ttu-id="9cac7-273">Java 클라이언트에서의 `withTransport` 메서드 `HttpHubConnectionBuilder`를 사용 하 여 전송이 선택 됩니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-273">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="9cac7-274">Java 클라이언트는 기본적으로 Websocket 전송을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-274">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="9cac7-275">SignalR Java 클라이언트는 아직 전송 대체를 지원 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-275">The SignalR Java client doesn't support transport fallback yet.</span></span>

::: moniker-end

### <a name="configure-bearer-authentication"></a><span data-ttu-id="9cac7-276">전달자 인증 구성</span><span class="sxs-lookup"><span data-stu-id="9cac7-276">Configure bearer authentication</span></span>

<span data-ttu-id="9cac7-277">SignalR 요청과 함께 인증 데이터를 제공하려면 `AccessTokenProvider` 옵션(JavaScript에서는 `accessTokenFactory`를)을 이용해서 원하는 액세스 토큰을 반환하는 함수를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-277">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="9cac7-278">.NET 클라이언트에서는 액세스 토큰이 HTTP "Bearer Authentication" 토큰으로 전달됩니다(`Bearer` 형식의 `Authorization` 헤더를 통해).</span><span class="sxs-lookup"><span data-stu-id="9cac7-278">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="9cac7-279">JavaScript 클라이언트에서는 브라우저의 API가 헤더 적용 기능을 제한하는 몇 가지 경우를 **제외**하면 (특히 서버-전송 이벤트 및 WebSockets 요청) 액세스 토큰이 전달자 토큰으로 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-279">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="9cac7-280">이런 경우 액세스 토큰은 `access_token`이라는 쿼리 문자열 값으로 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-280">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="9cac7-281">.NET 클라이언트에서 `AccessTokenProvider` 옵션은 `WithUrl`에서 옵션 대리자를 이용해서 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-281">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="9cac7-282">JavaScript 클라이언트에서는 `withUrl`에서 옵션 개체의 `accessTokenFactory` 필드를 설정하여 액세스 토큰을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-282">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="9cac7-283">SignalR Java 클라이언트에서 [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)에 액세스 토큰 팩터리를 제공 하 여 인증에 사용할 전달자 토큰을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-283">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="9cac7-284">[WithAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) 를 사용 하 여 [rxjava](https://github.com/ReactiveX/RxJava) [단일\<문자열 >](https://reactivex.io/documentation/single.html)를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-284">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="9cac7-285">[단일. 지연](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)에 대 한 호출을 사용 하 여 클라이언트에 대 한 액세스 토큰을 생성 하는 논리를 작성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-285">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="9cac7-286">시간 제한 및 연결 유지 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="9cac7-286">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="9cac7-287">시간 제한 및 연결 유지 동작을 구성하기 위한 추가 옵션은 `HubConnection` 개체 자체에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-287">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>


::: moniker range=">= aspnetcore-2.2"

# <a name="nettabdotnet"></a>[<span data-ttu-id="9cac7-288">.NET</span><span class="sxs-lookup"><span data-stu-id="9cac7-288">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="9cac7-289">옵션</span><span class="sxs-lookup"><span data-stu-id="9cac7-289">Option</span></span> | <span data-ttu-id="9cac7-290">기본값</span><span class="sxs-lookup"><span data-stu-id="9cac7-290">Default value</span></span> | <span data-ttu-id="9cac7-291">Description</span><span class="sxs-lookup"><span data-stu-id="9cac7-291">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="9cac7-292">30초(30000밀리초)</span><span class="sxs-lookup"><span data-stu-id="9cac7-292">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="9cac7-293">서버 활동 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-293">Timeout for server activity.</span></span> <span data-ttu-id="9cac7-294">서버가 이 시간 제한 내에 메시지를 전송하지 않으면 클라이언트는 서버 연결이 끊어졌다고 간주하고 `Closed` 이벤트(JavaScript에서는 `onclose` 이벤트)를  트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-294">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="9cac7-295">이 값이 충분히 커야만 ping 메시지가 서버에서 전송**되고** 시간 제한 내에 클라이언트에 의해 수신될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-295">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="9cac7-296">권장 값은 ping이 도착할 때까지 허용 하는 서버 `KeepAliveInterval` 값을 두 번 이상 사용 하는 숫자입니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-296">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="9cac7-297">15초</span><span class="sxs-lookup"><span data-stu-id="9cac7-297">15 seconds</span></span> | <span data-ttu-id="9cac7-298">초기 서버 핸드셰이크에 대한 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-298">Timeout for initial server handshake.</span></span> <span data-ttu-id="9cac7-299">서버가 이 시간 제한 내에 핸드셰이크 응답을 전송하지 않으면 클라이언트는 핸드셰이크를 취소하고 `Closed` 이벤트(JavaScript에서는 `onclose` 이벤트)를 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-299">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="9cac7-300">이 설정은 심각한 네트워크 지연으로 인해 핸드셰이크 시간 제한 오류가 발생하는 경우에만 수정해야 하는 고급 설정입니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-300">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="9cac7-301">핸드셰이크 프로세스에 대한 보다 자세한 내용은 [SignalR 허브 프로토콜 사양](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)을 참고하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-301">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="9cac7-302">15초</span><span class="sxs-lookup"><span data-stu-id="9cac7-302">15 seconds</span></span> | <span data-ttu-id="9cac7-303">클라이언트에서 ping 메시지를 보내는 간격을 결정 합니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-303">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="9cac7-304">클라이언트에서 메시지를 보내면 타이머를 간격의 시작으로 다시 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-304">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="9cac7-305">클라이언트에서 서버의 `ClientTimeoutInterval` 집합에 있는 메시지를 보내지 않은 경우 서버는 클라이언트의 연결을 끊은 것으로 간주 합니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-305">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="9cac7-306">.NET 클라이언트에서 시간 제한 값은 `TimeSpan` 값으로 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-306">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascripttabjavascript"></a>[<span data-ttu-id="9cac7-307">JavaScript</span><span class="sxs-lookup"><span data-stu-id="9cac7-307">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="9cac7-308">옵션</span><span class="sxs-lookup"><span data-stu-id="9cac7-308">Option</span></span> | <span data-ttu-id="9cac7-309">기본값</span><span class="sxs-lookup"><span data-stu-id="9cac7-309">Default value</span></span> | <span data-ttu-id="9cac7-310">설명</span><span class="sxs-lookup"><span data-stu-id="9cac7-310">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="9cac7-311">30초(30000밀리초)</span><span class="sxs-lookup"><span data-stu-id="9cac7-311">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="9cac7-312">서버 활동 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-312">Timeout for server activity.</span></span> <span data-ttu-id="9cac7-313">서버가이 간격 내에 메시지를 보내지 않은 경우 클라이언트는 서버 연결을 끊고 이벤트를 `onclose` 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-313">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="9cac7-314">이 값이 충분히 커야만 ping 메시지가 서버에서 전송**되고** 시간 제한 내에 클라이언트에 의해 수신될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-314">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="9cac7-315">권장 값은 ping이 도착할 때까지 허용 하는 서버 `KeepAliveInterval` 값을 두 번 이상 사용 하는 숫자입니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-315">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="9cac7-316">15 초 (15000 밀리초)</span><span class="sxs-lookup"><span data-stu-id="9cac7-316">15 seconds (15,000 miliseconds)</span></span> | <span data-ttu-id="9cac7-317">클라이언트에서 ping 메시지를 보내는 간격을 결정 합니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-317">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="9cac7-318">클라이언트에서 메시지를 보내면 타이머를 간격의 시작으로 다시 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-318">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="9cac7-319">클라이언트에서 서버의 `ClientTimeoutInterval` 집합에 있는 메시지를 보내지 않은 경우 서버는 클라이언트의 연결을 끊은 것으로 간주 합니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-319">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="javatabjava"></a>[<span data-ttu-id="9cac7-320">Java</span><span class="sxs-lookup"><span data-stu-id="9cac7-320">Java</span></span>](#tab/java)

| <span data-ttu-id="9cac7-321">옵션</span><span class="sxs-lookup"><span data-stu-id="9cac7-321">Option</span></span> | <span data-ttu-id="9cac7-322">기본값</span><span class="sxs-lookup"><span data-stu-id="9cac7-322">Default value</span></span> | <span data-ttu-id="9cac7-323">Description</span><span class="sxs-lookup"><span data-stu-id="9cac7-323">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="9cac7-324">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="9cac7-324">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="9cac7-325">30초(30000밀리초)</span><span class="sxs-lookup"><span data-stu-id="9cac7-325">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="9cac7-326">서버 활동 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-326">Timeout for server activity.</span></span> <span data-ttu-id="9cac7-327">서버가이 간격 내에 메시지를 보내지 않은 경우 클라이언트는 서버 연결을 끊고 이벤트를 `onClose` 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-327">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="9cac7-328">이 값이 충분히 커야만 ping 메시지가 서버에서 전송**되고** 시간 제한 내에 클라이언트에 의해 수신될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-328">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="9cac7-329">권장 값은 ping이 도착할 때까지 허용 하는 서버 `KeepAliveInterval` 값을 두 번 이상 사용 하는 숫자입니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-329">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="9cac7-330">15초</span><span class="sxs-lookup"><span data-stu-id="9cac7-330">15 seconds</span></span> | <span data-ttu-id="9cac7-331">초기 서버 핸드셰이크에 대한 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-331">Timeout for initial server handshake.</span></span> <span data-ttu-id="9cac7-332">서버가이 간격 내에 핸드셰이크 응답을 보내지 않는 경우 클라이언트는 핸드셰이크를 취소 하 고 이벤트를 `onClose` 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-332">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="9cac7-333">이 설정은 심각한 네트워크 지연으로 인해 핸드셰이크 시간 제한 오류가 발생하는 경우에만 수정해야 하는 고급 설정입니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-333">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="9cac7-334">핸드셰이크 프로세스에 대한 자세한 내용은 [SignalR 허브 프로토콜 사양](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)을 참고하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-334">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="9cac7-335">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="9cac7-335">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="9cac7-336">15 초 (15000 밀리초)</span><span class="sxs-lookup"><span data-stu-id="9cac7-336">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="9cac7-337">클라이언트에서 ping 메시지를 보내는 간격을 결정 합니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-337">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="9cac7-338">클라이언트에서 메시지를 보내면 타이머를 간격의 시작으로 다시 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-338">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="9cac7-339">클라이언트에서 서버의 `ClientTimeoutInterval` 집합에 있는 메시지를 보내지 않은 경우 서버는 클라이언트의 연결을 끊은 것으로 간주 합니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-339">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

::: moniker-end

::: moniker range="< aspnetcore-2.2"

# <a name="nettabdotnet"></a>[<span data-ttu-id="9cac7-340">.NET</span><span class="sxs-lookup"><span data-stu-id="9cac7-340">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="9cac7-341">옵션</span><span class="sxs-lookup"><span data-stu-id="9cac7-341">Option</span></span> | <span data-ttu-id="9cac7-342">기본값</span><span class="sxs-lookup"><span data-stu-id="9cac7-342">Default value</span></span> | <span data-ttu-id="9cac7-343">Description</span><span class="sxs-lookup"><span data-stu-id="9cac7-343">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="9cac7-344">30초(30000밀리초)</span><span class="sxs-lookup"><span data-stu-id="9cac7-344">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="9cac7-345">서버 활동 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-345">Timeout for server activity.</span></span> <span data-ttu-id="9cac7-346">서버가 이 시간 제한 내에 메시지를 전송하지 않으면 클라이언트는 서버 연결이 끊어졌다고 간주하고 `Closed` 이벤트(JavaScript에서는 `onclose` 이벤트)를  트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-346">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="9cac7-347">이 값이 충분히 커야만 ping 메시지가 서버에서 전송**되고** 시간 제한 내에 클라이언트에 의해 수신될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-347">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="9cac7-348">권장 값은 ping이 도착할 때까지 허용 하는 서버 `KeepAliveInterval` 값을 두 번 이상 사용 하는 숫자입니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-348">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="9cac7-349">15초</span><span class="sxs-lookup"><span data-stu-id="9cac7-349">15 seconds</span></span> | <span data-ttu-id="9cac7-350">초기 서버 핸드셰이크에 대한 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-350">Timeout for initial server handshake.</span></span> <span data-ttu-id="9cac7-351">서버가 이 시간 제한 내에 핸드셰이크 응답을 전송하지 않으면 클라이언트는 핸드셰이크를 취소하고 `Closed` 이벤트(JavaScript에서는 `onclose` 이벤트)를 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-351">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="9cac7-352">이 설정은 심각한 네트워크 지연으로 인해 핸드셰이크 시간 제한 오류가 발생하는 경우에만 수정해야 하는 고급 설정입니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-352">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="9cac7-353">핸드셰이크 프로세스에 대한 보다 자세한 내용은 [SignalR 허브 프로토콜 사양](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)을 참고하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-353">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

<span data-ttu-id="9cac7-354">.NET 클라이언트에서 시간 제한 값은 `TimeSpan` 값으로 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-354">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascripttabjavascript"></a>[<span data-ttu-id="9cac7-355">JavaScript</span><span class="sxs-lookup"><span data-stu-id="9cac7-355">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="9cac7-356">옵션</span><span class="sxs-lookup"><span data-stu-id="9cac7-356">Option</span></span> | <span data-ttu-id="9cac7-357">기본값</span><span class="sxs-lookup"><span data-stu-id="9cac7-357">Default value</span></span> | <span data-ttu-id="9cac7-358">설명</span><span class="sxs-lookup"><span data-stu-id="9cac7-358">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="9cac7-359">30초(30000밀리초)</span><span class="sxs-lookup"><span data-stu-id="9cac7-359">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="9cac7-360">서버 활동 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-360">Timeout for server activity.</span></span> <span data-ttu-id="9cac7-361">서버가이 간격 내에 메시지를 보내지 않은 경우 클라이언트는 서버 연결을 끊고 이벤트를 `onclose` 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-361">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="9cac7-362">이 값이 충분히 커야만 ping 메시지가 서버에서 전송**되고** 시간 제한 내에 클라이언트에 의해 수신될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-362">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="9cac7-363">권장 값은 ping이 도착할 때까지 허용 하는 서버 `KeepAliveInterval` 값을 두 번 이상 사용 하는 숫자입니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-363">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |

# <a name="javatabjava"></a>[<span data-ttu-id="9cac7-364">Java</span><span class="sxs-lookup"><span data-stu-id="9cac7-364">Java</span></span>](#tab/java)

| <span data-ttu-id="9cac7-365">옵션</span><span class="sxs-lookup"><span data-stu-id="9cac7-365">Option</span></span> | <span data-ttu-id="9cac7-366">기본값</span><span class="sxs-lookup"><span data-stu-id="9cac7-366">Default value</span></span> | <span data-ttu-id="9cac7-367">Description</span><span class="sxs-lookup"><span data-stu-id="9cac7-367">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="9cac7-368">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="9cac7-368">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="9cac7-369">30초(30000밀리초)</span><span class="sxs-lookup"><span data-stu-id="9cac7-369">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="9cac7-370">서버 활동 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-370">Timeout for server activity.</span></span> <span data-ttu-id="9cac7-371">서버가이 간격 내에 메시지를 보내지 않은 경우 클라이언트는 서버 연결을 끊고 이벤트를 `onClose` 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-371">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="9cac7-372">이 값이 충분히 커야만 ping 메시지가 서버에서 전송**되고** 시간 제한 내에 클라이언트에 의해 수신될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-372">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="9cac7-373">권장되는 값은 ping이 도착할 시간을 확보하기 위해 최소한 서버의 `KeepAliveInterval` 값의 두 배가 되는 숫자여야 합니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-373">The recommended value is a number at least double the server's `KeepAliveInterval` value, to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="9cac7-374">15초</span><span class="sxs-lookup"><span data-stu-id="9cac7-374">15 seconds</span></span> | <span data-ttu-id="9cac7-375">초기 서버 핸드셰이크에 대한 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-375">Timeout for initial server handshake.</span></span> <span data-ttu-id="9cac7-376">서버가이 간격 내에 핸드셰이크 응답을 보내지 않는 경우 클라이언트는 핸드셰이크를 취소 하 고 이벤트를 `onClose` 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-376">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="9cac7-377">이 설정은 심각한 네트워크 지연으로 인해 핸드셰이크 시간 제한 오류가 발생하는 경우에만 수정해야 하는 고급 설정입니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-377">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="9cac7-378">핸드셰이크 프로세스에 대한 보다 자세한 내용은 [SignalR 허브 프로토콜 사양](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)을 참고하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-378">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

::: moniker-end

---

### <a name="configure-additional-options"></a><span data-ttu-id="9cac7-379">추가 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="9cac7-379">Configure additional options</span></span>

<span data-ttu-id="9cac7-380">Java 클라이언트 `WithUrl` `HttpHubConnectionBuilder` 의에 있는 다양 한 구성`withUrl` api에서 또는의 `HubConnectionBuilder` (JavaScript) 메서드에서 추가 옵션을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-380">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="nettabdotnet"></a>[<span data-ttu-id="9cac7-381">.NET</span><span class="sxs-lookup"><span data-stu-id="9cac7-381">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="9cac7-382">.NET 옵션</span><span class="sxs-lookup"><span data-stu-id="9cac7-382">.NET Option</span></span> |  <span data-ttu-id="9cac7-383">기본값</span><span class="sxs-lookup"><span data-stu-id="9cac7-383">Default value</span></span> | <span data-ttu-id="9cac7-384">설명</span><span class="sxs-lookup"><span data-stu-id="9cac7-384">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="9cac7-385">HTTP 요청에서 전달자 인증 토큰으로 제공된 문자열을 반환하는 함수입니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-385">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="9cac7-386">이 값을 `true`로 설정하면 협상 단계를 건너뜁니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-386">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="9cac7-387">**WebSockets 전송이 유일하게 활성화된 전송인 경우에만 지원됩니다.**</span><span class="sxs-lookup"><span data-stu-id="9cac7-387">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="9cac7-388">Azure SignalR Service를 사용하는 경우에는 이 설정을 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-388">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="9cac7-389">Empty</span><span class="sxs-lookup"><span data-stu-id="9cac7-389">Empty</span></span> | <span data-ttu-id="9cac7-390">요청을 인증하기 위해 전송할 TLS 인증서의 컬렉션입니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-390">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="9cac7-391">Empty</span><span class="sxs-lookup"><span data-stu-id="9cac7-391">Empty</span></span> | <span data-ttu-id="9cac7-392">모든 HTTP 요청과 함께 전송할 HTTP 쿠키의 컬렉션입니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-392">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="9cac7-393">Empty</span><span class="sxs-lookup"><span data-stu-id="9cac7-393">Empty</span></span> | <span data-ttu-id="9cac7-394">모든 HTTP 요청과 함께 전송할 자격 증명입니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-394">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="9cac7-395">5초</span><span class="sxs-lookup"><span data-stu-id="9cac7-395">5 seconds</span></span> | <span data-ttu-id="9cac7-396">Websocket에만 해당됩니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-396">WebSockets only.</span></span> <span data-ttu-id="9cac7-397">클라이언트가 서버를 닫은 후 닫기 요청을 확인하기 위해 대기하는 최대 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-397">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="9cac7-398">서버가 이 시간 내에 종료를 승인하지 않으면 클라이언트가 연결을 끊습니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-398">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="9cac7-399">Empty</span><span class="sxs-lookup"><span data-stu-id="9cac7-399">Empty</span></span> | <span data-ttu-id="9cac7-400">모든 HTTP 요청과 함께 보낼 추가 HTTP 헤더의 맵입니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-400">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="9cac7-401">HTTP 요청을 전송하기 위해 사용되는 `HttpMessageHandler`를 구성하거나 대체하는 데 사용할 수 있는 대리자입니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-401">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="9cac7-402">WebSocket 연결에는 사용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-402">Not used for WebSocket connections.</span></span> <span data-ttu-id="9cac7-403">이 대리자는 null이 아닌 값을 반환해야 하며 매개 변수로 기본값을 받습니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-403">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="9cac7-404">이 기본값의 설정을 수정하여 반환하거나 새로운 `HttpMessageHandler` 인스턴스를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-404">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="9cac7-405">**처리기를 대체할 때 제공 된 처리기에서 유지할 설정을 복사 해야 합니다. 그렇지 않으면 구성 된 옵션 (예: 쿠키 및 헤더)이 새 처리기에 적용 되지 않습니다.**</span><span class="sxs-lookup"><span data-stu-id="9cac7-405">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="9cac7-406">HTTP 요청을 전송할 때 사용할 HTTP 프록시입니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-406">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="9cac7-407">HTTP 및 WebSockets 요청에 대한 기본 자격 증명을 전송하려면 이 부울 값을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-407">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="9cac7-408">그러면 Windows 인증을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-408">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="9cac7-409">추가적인 WebSocket 옵션을 구성할 수 있는 대리자입니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-409">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="9cac7-410">옵션을 구성에 사용할 수 있는 [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions)의 인스턴스를 전달받습니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-410">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascripttabjavascript"></a>[<span data-ttu-id="9cac7-411">JavaScript</span><span class="sxs-lookup"><span data-stu-id="9cac7-411">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="9cac7-412">JavaScript 옵션</span><span class="sxs-lookup"><span data-stu-id="9cac7-412">JavaScript Option</span></span> | <span data-ttu-id="9cac7-413">기본값</span><span class="sxs-lookup"><span data-stu-id="9cac7-413">Default Value</span></span> | <span data-ttu-id="9cac7-414">설명</span><span class="sxs-lookup"><span data-stu-id="9cac7-414">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="9cac7-415">HTTP 요청에서 전달자 인증 토큰으로 제공된 문자열을 반환하는 함수입니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-415">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="9cac7-416">이 값을 `true`로 설정하면 협상 단계를 건너뜁니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-416">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="9cac7-417">**WebSockets 전송이 유일하게 활성화된 전송인 경우에만 지원됩니다.**</span><span class="sxs-lookup"><span data-stu-id="9cac7-417">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="9cac7-418">Azure SignalR Service를 사용하는 경우에는 이 설정을 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-418">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="javatabjava"></a>[<span data-ttu-id="9cac7-419">Java</span><span class="sxs-lookup"><span data-stu-id="9cac7-419">Java</span></span>](#tab/java)

| <span data-ttu-id="9cac7-420">Java 옵션</span><span class="sxs-lookup"><span data-stu-id="9cac7-420">Java Option</span></span> | <span data-ttu-id="9cac7-421">기본값</span><span class="sxs-lookup"><span data-stu-id="9cac7-421">Default Value</span></span> | <span data-ttu-id="9cac7-422">설명</span><span class="sxs-lookup"><span data-stu-id="9cac7-422">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="9cac7-423">HTTP 요청에서 전달자 인증 토큰으로 제공된 문자열을 반환하는 함수입니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-423">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="9cac7-424">이 값을 `true`로 설정하면 협상 단계를 건너뜁니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-424">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="9cac7-425">**WebSockets 전송이 유일하게 활성화된 전송인 경우에만 지원됩니다.**</span><span class="sxs-lookup"><span data-stu-id="9cac7-425">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="9cac7-426">Azure SignalR Service를 사용하는 경우에는 이 설정을 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-426">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="9cac7-427">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="9cac7-427">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="9cac7-428">Empty</span><span class="sxs-lookup"><span data-stu-id="9cac7-428">Empty</span></span> | <span data-ttu-id="9cac7-429">모든 HTTP 요청과 함께 보낼 추가 HTTP 헤더의 맵입니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-429">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="9cac7-430">.NET 클라이언트에서 이 옵션들은 `WithUrl`에 제공되는 옵션 대리자를 이용해서 수정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-430">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="9cac7-431">JavaScript 클라이언트에서 이 옵션들은 `withUrl`에 제공되는 JavaScript 개체를 통해서 제공될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9cac7-431">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="9cac7-432">Java 클라이언트에서 다음에서 `HttpHubConnectionBuilder` 반환 된의 메서드를 사용 하 여 이러한 옵션을 구성할 수 있습니다.`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="9cac7-432">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="9cac7-433">추가 자료</span><span class="sxs-lookup"><span data-stu-id="9cac7-433">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>
