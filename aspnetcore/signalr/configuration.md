---
title: ASP.NET Core SignalR 구성
author: bradygaster
description: ASP.NET Core SignalR 앱을 구성 하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- SignalR
uid: signalr/configuration
ms.openlocfilehash: 682cc36216a4dc9b38c87b4f67100ab565a70e5c
ms.sourcegitcommit: 3fc3020961e1289ee5bf5f3c365ce8304d8ebf19
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73963978"
---
# <a name="aspnet-core-opno-locsignalr-configuration"></a><span data-ttu-id="43679-103">ASP.NET Core SignalR 구성</span><span class="sxs-lookup"><span data-stu-id="43679-103">ASP.NET Core SignalR configuration</span></span>

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="43679-104">JSON/MessagePack serialization 옵션</span><span class="sxs-lookup"><span data-stu-id="43679-104">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="43679-105">ASP.NET Core SignalR는 메시지 인코딩에 [JSON](https://www.json.org/) 및 [MessagePack](https://msgpack.org/index.html)의 두 가지 프로토콜을 지원 합니다.</span><span class="sxs-lookup"><span data-stu-id="43679-105">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="43679-106">각 프로토콜에는 serialization 구성 옵션이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="43679-106">Each protocol has serialization configuration options.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="43679-107">JSON serialization은 [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) 확장 메서드를 사용 하 여 서버에서 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="43679-107">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="43679-108">`AddJsonProtocol`는 `Startup.ConfigureServices`[AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) 뒤에 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="43679-108">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="43679-109">`AddJsonProtocol` 메서드는 `options` 개체를 받는 대리자를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="43679-109">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="43679-110">해당 개체의 [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) 속성은 인수 및 반환 값의 serialization을 구성 하는 데 사용할 수 있는 `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> 개체입니다.</span><span class="sxs-lookup"><span data-stu-id="43679-110">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="43679-111">자세한 내용은 [system.object 설명서](/dotnet/api/system.text.json)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="43679-111">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="43679-112">예를 들어 기본 "camelCase" 이름 대신 속성 이름의 대/소문자를 변경 하지 않도록 serializer를 구성 하려면 `Startup.ConfigureServices`에서 다음 코드를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="43679-112">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="43679-113">.NET 클라이언트에서는 [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)에 동일한 `AddJsonProtocol` 확장 메서드가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="43679-113">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="43679-114">확장 메서드를 확인 하려면 `Microsoft.Extensions.DependencyInjection` 네임 스페이스를 가져와야 합니다.</span><span class="sxs-lookup"><span data-stu-id="43679-114">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="43679-115">지금은 JavaScript 클라이언트에서 JSON serialization을 구성할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="43679-115">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="43679-116">Newtonsoft.json로 전환 합니다.</span><span class="sxs-lookup"><span data-stu-id="43679-116">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="43679-117">`System.Text.Json`에서 지원 되지 않는 `Newtonsoft.Json` 기능이 필요한 경우 [newtonsoft.json으로 전환](xref:migration/22-to-30#switch-to-newtonsoftjson)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="43679-117">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="43679-118">[AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) 확장 메서드를 사용 하 여 서버에서 JSON serialization을 구성할 수 있습니다 .이 메서드는 `Startup.ConfigureServices` 메서드에서 [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) 뒤에 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="43679-118">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="43679-119">`AddJsonProtocol` 메서드는 `options` 개체를 받는 대리자를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="43679-119">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="43679-120">해당 개체의 [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) 속성은 인수 및 반환 값의 serialization을 구성 하는 데 사용할 수 있는 JSON.NET `JsonSerializerSettings` 개체입니다.</span><span class="sxs-lookup"><span data-stu-id="43679-120">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="43679-121">자세한 내용은 [JSON.NET 설명서](https://www.newtonsoft.com/json/help/html/Introduction.htm)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="43679-121">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="43679-122">예를 들어 기본 "camelCase" 이름 대신 "대/소문자 구분" 속성 이름을 사용 하도록 serializer를 구성 하려면 `Startup.ConfigureServices`에서 다음 코드를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="43679-122">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="43679-123">.NET 클라이언트에서는 [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)에 동일한 `AddJsonProtocol` 확장 메서드가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="43679-123">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="43679-124">확장 메서드를 확인 하려면 `Microsoft.Extensions.DependencyInjection` 네임 스페이스를 가져와야 합니다.</span><span class="sxs-lookup"><span data-stu-id="43679-124">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="43679-125">지금은 JavaScript 클라이언트에서 JSON serialization을 구성할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="43679-125">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

::: moniker-end

### <a name="messagepack-serialization-options"></a><span data-ttu-id="43679-126">MessagePack serialization 옵션</span><span class="sxs-lookup"><span data-stu-id="43679-126">MessagePack serialization options</span></span>

<span data-ttu-id="43679-127">MessagePack serialization은 [Addmessagepackprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) 호출에 대리자를 제공 하 여 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="43679-127">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="43679-128">자세한 내용은 [SignalR의 MessagePack를](xref:signalr/messagepackhubprotocol) 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="43679-128">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="43679-129">이 시점에서는 JavaScript 클라이언트에서 MessagePack serialization을 구성할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="43679-129">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="43679-130">서버 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="43679-130">Configure server options</span></span>

<span data-ttu-id="43679-131">다음 표에서 SignalR 허브를 구성 하는 옵션을 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="43679-131">The following table describes options for configuring SignalR hubs:</span></span>

::: moniker range=">= aspnetcore-3.0"

| <span data-ttu-id="43679-132">옵션</span><span class="sxs-lookup"><span data-stu-id="43679-132">Option</span></span> | <span data-ttu-id="43679-133">기본값</span><span class="sxs-lookup"><span data-stu-id="43679-133">Default Value</span></span> | <span data-ttu-id="43679-134">설명</span><span class="sxs-lookup"><span data-stu-id="43679-134">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="43679-135">30초</span><span class="sxs-lookup"><span data-stu-id="43679-135">30 seconds</span></span> | <span data-ttu-id="43679-136">서버는이 간격 내에 메시지를 받지 못한 경우 (keep-alive 포함) 클라이언트의 연결을 해제 하는 것으로 간주 합니다.</span><span class="sxs-lookup"><span data-stu-id="43679-136">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="43679-137">이를 구현 하는 방법으로 인해 클라이언트에서 실제로 연결이 끊어진 것으로 표시 되는 시간 제한 간격 보다 오래 걸릴 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="43679-137">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="43679-138">권장 값은 두 개의 `KeepAliveInterval` 값입니다.</span><span class="sxs-lookup"><span data-stu-id="43679-138">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="43679-139">15초</span><span class="sxs-lookup"><span data-stu-id="43679-139">15 seconds</span></span> | <span data-ttu-id="43679-140">클라이언트에서이 시간 간격 내에 초기 핸드셰이크 메시지를 보내지 않으면 연결이 닫힙니다.</span><span class="sxs-lookup"><span data-stu-id="43679-140">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="43679-141">심각한 네트워크 대기 시간으로 인해 핸드셰이크 시간 초과 오류가 발생 하는 경우에만 수정 해야 하는 고급 설정입니다.</span><span class="sxs-lookup"><span data-stu-id="43679-141">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="43679-142">핸드셰이크 프로세스에 대 한 자세한 내용은 [SignalR 허브 프로토콜 사양](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="43679-142">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="43679-143">15초</span><span class="sxs-lookup"><span data-stu-id="43679-143">15 seconds</span></span> | <span data-ttu-id="43679-144">서버에서이 간격 내에 메시지를 보내지 않은 경우 ping 메시지가 자동으로 전송 되어 연결이 계속 열려 있습니다.</span><span class="sxs-lookup"><span data-stu-id="43679-144">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="43679-145">`KeepAliveInterval`를 변경 하는 경우 클라이언트에서 `ServerTimeout`/`serverTimeoutInMilliseconds` 설정을 변경 합니다.</span><span class="sxs-lookup"><span data-stu-id="43679-145">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="43679-146">권장 되는 `ServerTimeout`/`serverTimeoutInMilliseconds` 값은 `KeepAliveInterval` 값 두 배가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="43679-146">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="43679-147">설치 된 모든 프로토콜</span><span class="sxs-lookup"><span data-stu-id="43679-147">All installed protocols</span></span> | <span data-ttu-id="43679-148">이 허브에서 지원 되는 프로토콜입니다.</span><span class="sxs-lookup"><span data-stu-id="43679-148">Protocols supported by this hub.</span></span> <span data-ttu-id="43679-149">기본적으로 서버에 등록 된 모든 프로토콜이 허용 되지만이 목록에서 프로토콜을 제거 하 여 개별 허브에 대 한 특정 프로토콜을 사용 하지 않도록 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="43679-149">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="43679-150">`true`경우 허브 메서드에서 예외가 throw 되 면 자세한 예외 메시지가 클라이언트에 반환 됩니다.</span><span class="sxs-lookup"><span data-stu-id="43679-150">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="43679-151">이러한 예외 메시지에는 중요 한 정보가 포함 될 수 있으므로 기본값은 `false`입니다.</span><span class="sxs-lookup"><span data-stu-id="43679-151">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="43679-152">클라이언트 업로드 스트림에 대해 버퍼링 할 수 있는 최대 항목 수입니다.</span><span class="sxs-lookup"><span data-stu-id="43679-152">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="43679-153">이 제한에 도달 하면 서버에서 스트림 항목을 처리할 때까지 호출 처리가 차단 됩니다.</span><span class="sxs-lookup"><span data-stu-id="43679-153">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="43679-154">32 KB</span><span class="sxs-lookup"><span data-stu-id="43679-154">32 KB</span></span> | <span data-ttu-id="43679-155">들어오는 단일 허브 메시지의 최대 크기입니다.</span><span class="sxs-lookup"><span data-stu-id="43679-155">Maximum size of a single incoming hub message.</span></span> |

::: moniker-end

::: moniker range="= aspnetcore-2.2"

| <span data-ttu-id="43679-156">옵션</span><span class="sxs-lookup"><span data-stu-id="43679-156">Option</span></span> | <span data-ttu-id="43679-157">기본값</span><span class="sxs-lookup"><span data-stu-id="43679-157">Default Value</span></span> | <span data-ttu-id="43679-158">설명</span><span class="sxs-lookup"><span data-stu-id="43679-158">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="43679-159">30초</span><span class="sxs-lookup"><span data-stu-id="43679-159">30 seconds</span></span> | <span data-ttu-id="43679-160">서버는이 간격 내에 메시지를 받지 못한 경우 (keep-alive 포함) 클라이언트의 연결을 해제 하는 것으로 간주 합니다.</span><span class="sxs-lookup"><span data-stu-id="43679-160">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="43679-161">이를 구현 하는 방법으로 인해 클라이언트에서 실제로 연결이 끊어진 것으로 표시 되는 시간 제한 간격 보다 오래 걸릴 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="43679-161">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="43679-162">권장 값은 두 개의 `KeepAliveInterval` 값입니다.</span><span class="sxs-lookup"><span data-stu-id="43679-162">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="43679-163">15초</span><span class="sxs-lookup"><span data-stu-id="43679-163">15 seconds</span></span> | <span data-ttu-id="43679-164">클라이언트에서이 시간 간격 내에 초기 핸드셰이크 메시지를 보내지 않으면 연결이 닫힙니다.</span><span class="sxs-lookup"><span data-stu-id="43679-164">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="43679-165">심각한 네트워크 대기 시간으로 인해 핸드셰이크 시간 초과 오류가 발생 하는 경우에만 수정 해야 하는 고급 설정입니다.</span><span class="sxs-lookup"><span data-stu-id="43679-165">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="43679-166">핸드셰이크 프로세스에 대 한 자세한 내용은 [SignalR 허브 프로토콜 사양](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="43679-166">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="43679-167">15초</span><span class="sxs-lookup"><span data-stu-id="43679-167">15 seconds</span></span> | <span data-ttu-id="43679-168">서버에서이 간격 내에 메시지를 보내지 않은 경우 ping 메시지가 자동으로 전송 되어 연결이 계속 열려 있습니다.</span><span class="sxs-lookup"><span data-stu-id="43679-168">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="43679-169">`KeepAliveInterval`를 변경 하는 경우 클라이언트에서 `ServerTimeout`/`serverTimeoutInMilliseconds` 설정을 변경 합니다.</span><span class="sxs-lookup"><span data-stu-id="43679-169">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="43679-170">권장 되는 `ServerTimeout`/`serverTimeoutInMilliseconds` 값은 `KeepAliveInterval` 값 두 배가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="43679-170">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="43679-171">설치 된 모든 프로토콜</span><span class="sxs-lookup"><span data-stu-id="43679-171">All installed protocols</span></span> | <span data-ttu-id="43679-172">이 허브에서 지원 되는 프로토콜입니다.</span><span class="sxs-lookup"><span data-stu-id="43679-172">Protocols supported by this hub.</span></span> <span data-ttu-id="43679-173">기본적으로 서버에 등록 된 모든 프로토콜이 허용 되지만이 목록에서 프로토콜을 제거 하 여 개별 허브에 대 한 특정 프로토콜을 사용 하지 않도록 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="43679-173">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="43679-174">`true`경우 허브 메서드에서 예외가 throw 되 면 자세한 예외 메시지가 클라이언트에 반환 됩니다.</span><span class="sxs-lookup"><span data-stu-id="43679-174">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="43679-175">이러한 예외 메시지에는 중요 한 정보가 포함 될 수 있으므로 기본값은 `false`입니다.</span><span class="sxs-lookup"><span data-stu-id="43679-175">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

::: moniker-end

::: moniker range="= aspnetcore-2.1"

| <span data-ttu-id="43679-176">옵션</span><span class="sxs-lookup"><span data-stu-id="43679-176">Option</span></span> | <span data-ttu-id="43679-177">기본값</span><span class="sxs-lookup"><span data-stu-id="43679-177">Default Value</span></span> | <span data-ttu-id="43679-178">설명</span><span class="sxs-lookup"><span data-stu-id="43679-178">Description</span></span> |
| ------ | ------------- | ----------- |
| `HandshakeTimeout` | <span data-ttu-id="43679-179">15초</span><span class="sxs-lookup"><span data-stu-id="43679-179">15 seconds</span></span> | <span data-ttu-id="43679-180">클라이언트에서이 시간 간격 내에 초기 핸드셰이크 메시지를 보내지 않으면 연결이 닫힙니다.</span><span class="sxs-lookup"><span data-stu-id="43679-180">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="43679-181">심각한 네트워크 대기 시간으로 인해 핸드셰이크 시간 초과 오류가 발생 하는 경우에만 수정 해야 하는 고급 설정입니다.</span><span class="sxs-lookup"><span data-stu-id="43679-181">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="43679-182">핸드셰이크 프로세스에 대 한 자세한 내용은 [SignalR 허브 프로토콜 사양](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="43679-182">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="43679-183">15초</span><span class="sxs-lookup"><span data-stu-id="43679-183">15 seconds</span></span> | <span data-ttu-id="43679-184">서버에서이 간격 내에 메시지를 보내지 않은 경우 ping 메시지가 자동으로 전송 되어 연결이 계속 열려 있습니다.</span><span class="sxs-lookup"><span data-stu-id="43679-184">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="43679-185">`KeepAliveInterval`를 변경 하는 경우 클라이언트에서 `ServerTimeout`/`serverTimeoutInMilliseconds` 설정을 변경 합니다.</span><span class="sxs-lookup"><span data-stu-id="43679-185">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="43679-186">권장 되는 `ServerTimeout`/`serverTimeoutInMilliseconds` 값은 `KeepAliveInterval` 값 두 배가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="43679-186">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="43679-187">설치 된 모든 프로토콜</span><span class="sxs-lookup"><span data-stu-id="43679-187">All installed protocols</span></span> | <span data-ttu-id="43679-188">이 허브에서 지원 되는 프로토콜입니다.</span><span class="sxs-lookup"><span data-stu-id="43679-188">Protocols supported by this hub.</span></span> <span data-ttu-id="43679-189">기본적으로 서버에 등록 된 모든 프로토콜이 허용 되지만이 목록에서 프로토콜을 제거 하 여 개별 허브에 대 한 특정 프로토콜을 사용 하지 않도록 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="43679-189">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="43679-190">`true`경우 허브 메서드에서 예외가 throw 되 면 자세한 예외 메시지가 클라이언트에 반환 됩니다.</span><span class="sxs-lookup"><span data-stu-id="43679-190">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="43679-191">이러한 예외 메시지에는 중요 한 정보가 포함 될 수 있으므로 기본값은 `false`입니다.</span><span class="sxs-lookup"><span data-stu-id="43679-191">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

::: moniker-end

<span data-ttu-id="43679-192">`Startup.ConfigureServices`에서 `AddSignalR` 호출에 옵션 대리자를 제공 하 여 모든 허브에 대 한 옵션을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="43679-192">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="43679-193">단일 허브에 대 한 옵션은 `AddSignalR`에서 제공 하는 전역 옵션을 재정의 하며 <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>를 사용 하 여 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="43679-193">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="43679-194">고급 HTTP 구성 옵션</span><span class="sxs-lookup"><span data-stu-id="43679-194">Advanced HTTP configuration options</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="43679-195">`HttpConnectionDispatcherOptions`를 사용 하 여 전송 및 메모리 버퍼 관리와 관련 된 고급 설정을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="43679-195">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="43679-196">이러한 옵션은 `Startup.Configure`에서 [\<t >의 Maphub](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) 에 대리자를 전달 하 여 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="43679-196">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="43679-197">`HttpConnectionDispatcherOptions`를 사용 하 여 전송 및 메모리 버퍼 관리와 관련 된 고급 설정을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="43679-197">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="43679-198">이러한 옵션은 `Startup.Configure`에서 [\<t >의 Maphub](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) 에 대리자를 전달 하 여 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="43679-198">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="43679-199">다음 표에서는 ASP.NET Core SignalR의 고급 HTTP 옵션을 구성 하는 옵션을 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="43679-199">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

::: moniker range=">= aspnetcore-3.0"

| <span data-ttu-id="43679-200">옵션</span><span class="sxs-lookup"><span data-stu-id="43679-200">Option</span></span> | <span data-ttu-id="43679-201">기본값</span><span class="sxs-lookup"><span data-stu-id="43679-201">Default Value</span></span> | <span data-ttu-id="43679-202">설명</span><span class="sxs-lookup"><span data-stu-id="43679-202">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="43679-203">32 KB</span><span class="sxs-lookup"><span data-stu-id="43679-203">32 KB</span></span> | <span data-ttu-id="43679-204">클라이언트에서 수신 된 백 압력을 적용 하기 전에 버퍼링 되는 최대 바이트 수입니다.</span><span class="sxs-lookup"><span data-stu-id="43679-204">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="43679-205">이 값을 늘리면 서버는 백 압력을 적용 하지 않고 더 큰 메시지를 더 빨리 수신할 수 있지만 메모리 사용을 높일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="43679-205">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="43679-206">허브 클래스에 적용 된 `Authorize` 특성에서 자동으로 수집 된 데이터입니다.</span><span class="sxs-lookup"><span data-stu-id="43679-206">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="43679-207">클라이언트에 허브에 연결할 수 있는 권한이 있는지 여부를 확인 하는 데 사용 되는 [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) 개체의 목록입니다.</span><span class="sxs-lookup"><span data-stu-id="43679-207">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="43679-208">32 KB</span><span class="sxs-lookup"><span data-stu-id="43679-208">32 KB</span></span> | <span data-ttu-id="43679-209">서버에서 백 압력을 관찰 하기 전에 버퍼링 하는 앱에서 보낸 최대 바이트 수입니다.</span><span class="sxs-lookup"><span data-stu-id="43679-209">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="43679-210">이 값을 늘리면 서버에서 백 압력을 대기 하지 않고 더 큰 메시지를 더 빨리 버퍼링 할 수 있지만 메모리 소비가 증가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="43679-210">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="43679-211">모든 전송을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="43679-211">All Transports are enabled.</span></span> | <span data-ttu-id="43679-212">클라이언트에서 연결 하는 데 사용할 수 있는 전송을 제한할 수 있는 `HttpTransportType` 값의 비트 플래그 열거형입니다.</span><span class="sxs-lookup"><span data-stu-id="43679-212">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="43679-213">다음을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="43679-213">See below.</span></span> | <span data-ttu-id="43679-214">긴 폴링 전송과 관련 된 추가 옵션입니다.</span><span class="sxs-lookup"><span data-stu-id="43679-214">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="43679-215">다음을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="43679-215">See below.</span></span> | <span data-ttu-id="43679-216">Websocket 전송과 관련 된 추가 옵션입니다.</span><span class="sxs-lookup"><span data-stu-id="43679-216">Additional options specific to the WebSockets transport.</span></span> |

::: moniker-end

::: moniker range="< aspnetcore-3.0"

| <span data-ttu-id="43679-217">옵션</span><span class="sxs-lookup"><span data-stu-id="43679-217">Option</span></span> | <span data-ttu-id="43679-218">기본값</span><span class="sxs-lookup"><span data-stu-id="43679-218">Default Value</span></span> | <span data-ttu-id="43679-219">설명</span><span class="sxs-lookup"><span data-stu-id="43679-219">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="43679-220">32 KB</span><span class="sxs-lookup"><span data-stu-id="43679-220">32 KB</span></span> | <span data-ttu-id="43679-221">서버에서 버퍼링 하는 클라이언트로부터 받은 최대 바이트 수입니다.</span><span class="sxs-lookup"><span data-stu-id="43679-221">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="43679-222">이 값을 늘리면 서버에서 더 큰 메시지를 받을 수 있지만 메모리 사용에 부정적인 영향을 줄 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="43679-222">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="43679-223">허브 클래스에 적용 된 `Authorize` 특성에서 자동으로 수집 된 데이터입니다.</span><span class="sxs-lookup"><span data-stu-id="43679-223">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="43679-224">클라이언트에 허브에 연결할 수 있는 권한이 있는지 여부를 확인 하는 데 사용 되는 [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) 개체의 목록입니다.</span><span class="sxs-lookup"><span data-stu-id="43679-224">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="43679-225">32 KB</span><span class="sxs-lookup"><span data-stu-id="43679-225">32 KB</span></span> | <span data-ttu-id="43679-226">서버가 버퍼링 하는 앱에서 보낸 최대 바이트 수입니다.</span><span class="sxs-lookup"><span data-stu-id="43679-226">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="43679-227">이 값을 늘리면 서버에서 더 큰 메시지를 보낼 수 있지만 메모리 사용에 부정적인 영향을 줄 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="43679-227">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="43679-228">모든 전송을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="43679-228">All Transports are enabled.</span></span> | <span data-ttu-id="43679-229">클라이언트에서 연결 하는 데 사용할 수 있는 전송을 제한할 수 있는 `HttpTransportType` 값의 비트 플래그 열거형입니다.</span><span class="sxs-lookup"><span data-stu-id="43679-229">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="43679-230">다음을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="43679-230">See below.</span></span> | <span data-ttu-id="43679-231">긴 폴링 전송과 관련 된 추가 옵션입니다.</span><span class="sxs-lookup"><span data-stu-id="43679-231">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="43679-232">다음을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="43679-232">See below.</span></span> | <span data-ttu-id="43679-233">Websocket 전송과 관련 된 추가 옵션입니다.</span><span class="sxs-lookup"><span data-stu-id="43679-233">Additional options specific to the WebSockets transport.</span></span> |

::: moniker-end

<span data-ttu-id="43679-234">긴 폴링 전송에는 `LongPolling` 속성을 사용 하 여 구성할 수 있는 추가 옵션이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="43679-234">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="43679-235">옵션</span><span class="sxs-lookup"><span data-stu-id="43679-235">Option</span></span> | <span data-ttu-id="43679-236">기본값</span><span class="sxs-lookup"><span data-stu-id="43679-236">Default Value</span></span> | <span data-ttu-id="43679-237">설명</span><span class="sxs-lookup"><span data-stu-id="43679-237">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="43679-238">90 초</span><span class="sxs-lookup"><span data-stu-id="43679-238">90 seconds</span></span> | <span data-ttu-id="43679-239">단일 폴링 요청을 종료 하기 전에 서버가 클라이언트에 보낼 메시지를 대기 하는 최대 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="43679-239">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="43679-240">이 값을 줄이면 클라이언트가 새 폴링 요청을 더 자주 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="43679-240">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="43679-241">WebSocket 전송은 `WebSockets` 속성을 사용 하 여 구성할 수 있는 추가 옵션을 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="43679-241">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="43679-242">옵션</span><span class="sxs-lookup"><span data-stu-id="43679-242">Option</span></span> | <span data-ttu-id="43679-243">기본값</span><span class="sxs-lookup"><span data-stu-id="43679-243">Default Value</span></span> | <span data-ttu-id="43679-244">설명</span><span class="sxs-lookup"><span data-stu-id="43679-244">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="43679-245">5초</span><span class="sxs-lookup"><span data-stu-id="43679-245">5 seconds</span></span> | <span data-ttu-id="43679-246">서버를 닫은 후 클라이언트를이 시간 간격 내에 닫지 못한 경우 연결이 종료 됩니다.</span><span class="sxs-lookup"><span data-stu-id="43679-246">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="43679-247">`Sec-WebSocket-Protocol` 헤더를 사용자 지정 값으로 설정 하는 데 사용할 수 있는 대리자입니다.</span><span class="sxs-lookup"><span data-stu-id="43679-247">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="43679-248">대리자는 클라이언트에서 입력으로 요청 된 값을 받고 원하는 값을 반환할 것으로 예상 합니다.</span><span class="sxs-lookup"><span data-stu-id="43679-248">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="43679-249">클라이언트 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="43679-249">Configure client options</span></span>

<span data-ttu-id="43679-250">클라이언트 옵션은 `HubConnectionBuilder` 형식 (.NET 및 JavaScript 클라이언트에서 사용 가능)에서 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="43679-250">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="43679-251">Java 클라이언트 에서도 사용할 수 있지만 `HttpHubConnectionBuilder` 하위 클래스는 작성기 구성 옵션과 `HubConnection` 자체를 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="43679-251">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="43679-252">로깅 구성</span><span class="sxs-lookup"><span data-stu-id="43679-252">Configure logging</span></span>

<span data-ttu-id="43679-253">로깅은 `ConfigureLogging` 메서드를 사용 하 여 .NET 클라이언트에서 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="43679-253">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="43679-254">로깅 공급자와 필터는 서버에 있는 것과 동일한 방식으로 등록할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="43679-254">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="43679-255">자세한 내용은 [ASP.NET Core의 로깅](xref:fundamentals/logging/index) 문서를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="43679-255">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="43679-256">로깅 공급자를 등록 하려면 필요한 패키지를 설치 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="43679-256">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="43679-257">전체 목록은 문서에서 [기본 제공 로깅 공급자](xref:fundamentals/logging/index#built-in-logging-providers) 섹션을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="43679-257">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="43679-258">예를 들어 콘솔 로깅을 사용 하도록 설정 하려면 `Microsoft.Extensions.Logging.Console` NuGet 패키지를 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="43679-258">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="43679-259">`AddConsole` 확장 메서드를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="43679-259">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="43679-260">JavaScript 클라이언트에서 유사한 `configureLogging` 메서드가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="43679-260">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="43679-261">생성할 로그 메시지의 최소 수준을 나타내는 `LogLevel` 값을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="43679-261">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="43679-262">로그는 브라우저 콘솔 창에 기록 됩니다.</span><span class="sxs-lookup"><span data-stu-id="43679-262">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="43679-263">`LogLevel` 값 대신 로그 수준 이름을 나타내는 `string` 값을 제공할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="43679-263">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="43679-264">이는 `LogLevel` 상수에 대 한 액세스 권한이 없는 환경에서 SignalR 로깅을 구성할 때 유용 합니다.</span><span class="sxs-lookup"><span data-stu-id="43679-264">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="43679-265">다음 표에서는 사용 가능한 로그 수준을 나열 합니다.</span><span class="sxs-lookup"><span data-stu-id="43679-265">The following table lists the available log levels.</span></span> <span data-ttu-id="43679-266">사용자가 `configureLogging`에 제공 하는 값은 로깅할 **최소** 로그 수준을 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="43679-266">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="43679-267">이 수준에서 기록 되 **는 메시지 또는 테이블의 이후 나열 된 수준**에 기록 됩니다.</span><span class="sxs-lookup"><span data-stu-id="43679-267">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="43679-268">String</span><span class="sxs-lookup"><span data-stu-id="43679-268">String</span></span>                      | <span data-ttu-id="43679-269">LogLevel</span><span class="sxs-lookup"><span data-stu-id="43679-269">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="43679-270">`info` **또는** `information`</span><span class="sxs-lookup"><span data-stu-id="43679-270">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="43679-271">`warn` **또는** `warning`</span><span class="sxs-lookup"><span data-stu-id="43679-271">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

::: moniker-end

> [!NOTE]
> <span data-ttu-id="43679-272">로깅을 완전히 사용 하지 않도록 설정 하려면 `configureLogging` 메서드에서 `signalR.LogLevel.None`를 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="43679-272">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="43679-273">로깅에 대 한 자세한 내용은 [SignalR 진단 설명서](xref:signalr/diagnostics)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="43679-273">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="43679-274">SignalR Java 클라이언트는 [SLF4J](https://www.slf4j.org/) 라이브러리를 사용 하 여 로깅을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="43679-274">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="43679-275">이는 라이브러리의 사용자가 특정 로깅 종속성을 가져와 자신의 특정 로깅 구현을 선택할 수 있게 해 주는 상위 수준 로깅 API입니다.</span><span class="sxs-lookup"><span data-stu-id="43679-275">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="43679-276">다음 코드 조각에서는 SignalR Java 클라이언트와 함께 `java.util.logging`를 사용 하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="43679-276">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="43679-277">종속성에서 로깅을 구성 하지 않으면 SLF4J는 다음과 같은 경고 메시지와 함께 기본 작업 없음로 거를 로드 합니다.</span><span class="sxs-lookup"><span data-stu-id="43679-277">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="43679-278">이는 무시 해도 됩니다.</span><span class="sxs-lookup"><span data-stu-id="43679-278">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="43679-279">허용 된 전송 구성</span><span class="sxs-lookup"><span data-stu-id="43679-279">Configure allowed transports</span></span>

<span data-ttu-id="43679-280">SignalR에서 사용 하는 전송은 `WithUrl` 호출 (JavaScript의`withUrl`)에서 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="43679-280">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="43679-281">`HttpTransportType` 값의 비트 or를 사용 하 여 지정 된 전송만 사용 하도록 클라이언트를 제한할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="43679-281">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="43679-282">모든 전송은 기본적으로 사용 하도록 설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="43679-282">All transports are enabled by default.</span></span>

<span data-ttu-id="43679-283">예를 들어 서버에서 보낸 이벤트 전송을 사용 하지 않도록 설정 하 고 Websocket 및 긴 폴링 연결을 허용 하려면 다음을 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="43679-283">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="43679-284">JavaScript 클라이언트에서 `withUrl`에 제공 된 options 개체의 `transport` 필드를 설정 하 여 전송이 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="43679-284">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="43679-285">이 버전의 Java 클라이언트 websocket은 유일 하 게 사용할 수 있는 전송입니다.</span><span class="sxs-lookup"><span data-stu-id="43679-285">In this version of the Java client websockets is the only available transport.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-3.0"

<span data-ttu-id="43679-286">Java 클라이언트에서 `HttpHubConnectionBuilder`의 `withTransport` 메서드를 사용 하 여 전송이 선택 됩니다.</span><span class="sxs-lookup"><span data-stu-id="43679-286">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="43679-287">Java 클라이언트는 기본적으로 Websocket 전송을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="43679-287">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="43679-288">SignalR Java 클라이언트는 아직 전송 대체를 지원 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="43679-288">The SignalR Java client doesn't support transport fallback yet.</span></span>

::: moniker-end

### <a name="configure-bearer-authentication"></a><span data-ttu-id="43679-289">전달자 인증 구성</span><span class="sxs-lookup"><span data-stu-id="43679-289">Configure bearer authentication</span></span>

<span data-ttu-id="43679-290">SignalR 요청과 함께 인증 데이터를 제공 하려면 JavaScript에서`accessTokenFactory` `AccessTokenProvider` 옵션을 사용 하 여 원하는 액세스 토큰을 반환 하는 함수를 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="43679-290">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="43679-291">.NET 클라이언트에서이 액세스 토큰은 HTTP "전달자 인증" 토큰으로 전달 됩니다 (`Bearer`형식으로 `Authorization` 헤더 사용).</span><span class="sxs-lookup"><span data-stu-id="43679-291">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="43679-292">JavaScript 클라이언트에서 액세스 토큰은 브라우저 Api가 헤더를 적용 하는 기능을 제한 하는 경우를 **제외** 하 고 전달자 토큰으로 사용 됩니다 (특히 서버에서 보낸 이벤트와 websocket 요청에서).</span><span class="sxs-lookup"><span data-stu-id="43679-292">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="43679-293">이러한 경우 액세스 토큰은 `access_token`쿼리 문자열 값으로 제공 됩니다.</span><span class="sxs-lookup"><span data-stu-id="43679-293">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="43679-294">.NET 클라이언트에서 `AccessTokenProvider` 옵션은 `WithUrl`의 옵션 대리자를 사용 하 여 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="43679-294">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="43679-295">JavaScript 클라이언트에서 액세스 토큰은 `withUrl`의 options 개체에 `accessTokenFactory` 필드를 설정 하 여 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="43679-295">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="43679-296">SignalR Java 클라이언트에서 [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)에 액세스 토큰 팩터리를 제공 하 여 인증에 사용할 전달자 토큰을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="43679-296">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="43679-297">[WithAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) 를 사용 하 여 [rxjava](https://github.com/ReactiveX/RxJava) [Single\<문자열 >](https://reactivex.io/documentation/single.html)를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="43679-297">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="43679-298">[단일. 지연](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)에 대 한 호출을 사용 하 여 클라이언트에 대 한 액세스 토큰을 생성 하는 논리를 작성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="43679-298">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="43679-299">제한 시간 및 연결 유지 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="43679-299">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="43679-300">제한 시간 및 연결 유지 동작을 구성 하기 위한 추가 옵션은 `HubConnection` 개체 자체에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="43679-300">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>


::: moniker range=">= aspnetcore-2.2"

# <a name="nettabdotnet"></a>[<span data-ttu-id="43679-301">.NET</span><span class="sxs-lookup"><span data-stu-id="43679-301">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="43679-302">옵션</span><span class="sxs-lookup"><span data-stu-id="43679-302">Option</span></span> | <span data-ttu-id="43679-303">기본값</span><span class="sxs-lookup"><span data-stu-id="43679-303">Default value</span></span> | <span data-ttu-id="43679-304">설명</span><span class="sxs-lookup"><span data-stu-id="43679-304">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="43679-305">30 초 (3만 밀리초)</span><span class="sxs-lookup"><span data-stu-id="43679-305">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="43679-306">서버 작업에 대 한 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="43679-306">Timeout for server activity.</span></span> <span data-ttu-id="43679-307">서버가이 간격 내에 메시지를 보내지 않은 경우 클라이언트는 서버 연결을 끊고 `Closed` 이벤트 (JavaScript의`onclose`)를 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="43679-307">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="43679-308">이 값은 ping 메시지 **를 서버에서 보내고 시간** 제한 간격 내에 클라이언트에서 받을 수 있도록 충분히 커야 합니다.</span><span class="sxs-lookup"><span data-stu-id="43679-308">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="43679-309">권장 값은 ping이 도착할 때까지 시간을 허용 하기 위해 서버의 `KeepAliveInterval` 값을 두 번 이상 사용 하는 숫자입니다.</span><span class="sxs-lookup"><span data-stu-id="43679-309">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="43679-310">15초</span><span class="sxs-lookup"><span data-stu-id="43679-310">15 seconds</span></span> | <span data-ttu-id="43679-311">초기 서버 핸드셰이크에 대 한 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="43679-311">Timeout for initial server handshake.</span></span> <span data-ttu-id="43679-312">서버에서이 간격으로 핸드셰이크 응답을 보내지 않는 경우 클라이언트는 핸드셰이크를 취소 하 고 `Closed` 이벤트 (JavaScript의`onclose`)를 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="43679-312">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="43679-313">심각한 네트워크 대기 시간으로 인해 핸드셰이크 시간 초과 오류가 발생 하는 경우에만 수정 해야 하는 고급 설정입니다.</span><span class="sxs-lookup"><span data-stu-id="43679-313">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="43679-314">핸드셰이크 프로세스에 대 한 자세한 내용은 [SignalR 허브 프로토콜 사양](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="43679-314">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="43679-315">15초</span><span class="sxs-lookup"><span data-stu-id="43679-315">15 seconds</span></span> | <span data-ttu-id="43679-316">클라이언트에서 ping 메시지를 보내는 간격을 결정 합니다.</span><span class="sxs-lookup"><span data-stu-id="43679-316">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="43679-317">클라이언트에서 메시지를 보내면 타이머를 간격의 시작으로 다시 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="43679-317">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="43679-318">클라이언트가 서버에 설정 된 `ClientTimeoutInterval`에서 메시지를 보내지 않은 경우 서버는 클라이언트의 연결을 끊은 것으로 간주 합니다.</span><span class="sxs-lookup"><span data-stu-id="43679-318">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="43679-319">.NET 클라이언트에서 시간 제한 값은 `TimeSpan` 값으로 지정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="43679-319">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascripttabjavascript"></a>[<span data-ttu-id="43679-320">JavaScript</span><span class="sxs-lookup"><span data-stu-id="43679-320">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="43679-321">옵션</span><span class="sxs-lookup"><span data-stu-id="43679-321">Option</span></span> | <span data-ttu-id="43679-322">기본값</span><span class="sxs-lookup"><span data-stu-id="43679-322">Default value</span></span> | <span data-ttu-id="43679-323">설명</span><span class="sxs-lookup"><span data-stu-id="43679-323">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="43679-324">30 초 (3만 밀리초)</span><span class="sxs-lookup"><span data-stu-id="43679-324">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="43679-325">서버 작업에 대 한 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="43679-325">Timeout for server activity.</span></span> <span data-ttu-id="43679-326">서버가이 간격 내에 메시지를 보내지 않은 경우 클라이언트는 서버 연결을 끊고 `onclose` 이벤트를 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="43679-326">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="43679-327">이 값은 ping 메시지 **를 서버에서 보내고 시간** 제한 간격 내에 클라이언트에서 받을 수 있도록 충분히 커야 합니다.</span><span class="sxs-lookup"><span data-stu-id="43679-327">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="43679-328">권장 값은 ping이 도착할 때까지 시간을 허용 하기 위해 서버의 `KeepAliveInterval` 값을 두 번 이상 사용 하는 숫자입니다.</span><span class="sxs-lookup"><span data-stu-id="43679-328">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="43679-329">15 초 (15000 밀리초)</span><span class="sxs-lookup"><span data-stu-id="43679-329">15 seconds (15,000 miliseconds)</span></span> | <span data-ttu-id="43679-330">클라이언트에서 ping 메시지를 보내는 간격을 결정 합니다.</span><span class="sxs-lookup"><span data-stu-id="43679-330">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="43679-331">클라이언트에서 메시지를 보내면 타이머를 간격의 시작으로 다시 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="43679-331">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="43679-332">클라이언트가 서버에 설정 된 `ClientTimeoutInterval`에서 메시지를 보내지 않은 경우 서버는 클라이언트의 연결을 끊은 것으로 간주 합니다.</span><span class="sxs-lookup"><span data-stu-id="43679-332">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="javatabjava"></a>[<span data-ttu-id="43679-333">Java</span><span class="sxs-lookup"><span data-stu-id="43679-333">Java</span></span>](#tab/java)

| <span data-ttu-id="43679-334">옵션</span><span class="sxs-lookup"><span data-stu-id="43679-334">Option</span></span> | <span data-ttu-id="43679-335">기본값</span><span class="sxs-lookup"><span data-stu-id="43679-335">Default value</span></span> | <span data-ttu-id="43679-336">설명</span><span class="sxs-lookup"><span data-stu-id="43679-336">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="43679-337">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="43679-337">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="43679-338">30 초 (3만 밀리초)</span><span class="sxs-lookup"><span data-stu-id="43679-338">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="43679-339">서버 작업에 대 한 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="43679-339">Timeout for server activity.</span></span> <span data-ttu-id="43679-340">서버가이 간격 내에 메시지를 보내지 않은 경우 클라이언트는 서버 연결을 끊고 `onClose` 이벤트를 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="43679-340">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="43679-341">이 값은 ping 메시지 **를 서버에서 보내고 시간** 제한 간격 내에 클라이언트에서 받을 수 있도록 충분히 커야 합니다.</span><span class="sxs-lookup"><span data-stu-id="43679-341">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="43679-342">권장 값은 ping이 도착할 때까지 시간을 허용 하기 위해 서버의 `KeepAliveInterval` 값을 두 번 이상 사용 하는 숫자입니다.</span><span class="sxs-lookup"><span data-stu-id="43679-342">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="43679-343">15초</span><span class="sxs-lookup"><span data-stu-id="43679-343">15 seconds</span></span> | <span data-ttu-id="43679-344">초기 서버 핸드셰이크에 대 한 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="43679-344">Timeout for initial server handshake.</span></span> <span data-ttu-id="43679-345">서버가이 간격 내에 핸드셰이크 응답을 보내지 않는 경우 클라이언트는 핸드셰이크를 취소 하 고 `onClose` 이벤트를 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="43679-345">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="43679-346">심각한 네트워크 대기 시간으로 인해 핸드셰이크 시간 초과 오류가 발생 하는 경우에만 수정 해야 하는 고급 설정입니다.</span><span class="sxs-lookup"><span data-stu-id="43679-346">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="43679-347">핸드셰이크 프로세스에 대 한 자세한 내용은 [SignalR 허브 프로토콜 사양](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="43679-347">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="43679-348">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="43679-348">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="43679-349">15 초 (15000 밀리초)</span><span class="sxs-lookup"><span data-stu-id="43679-349">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="43679-350">클라이언트에서 ping 메시지를 보내는 간격을 결정 합니다.</span><span class="sxs-lookup"><span data-stu-id="43679-350">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="43679-351">클라이언트에서 메시지를 보내면 타이머를 간격의 시작으로 다시 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="43679-351">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="43679-352">클라이언트가 서버에 설정 된 `ClientTimeoutInterval`에서 메시지를 보내지 않은 경우 서버는 클라이언트의 연결을 끊은 것으로 간주 합니다.</span><span class="sxs-lookup"><span data-stu-id="43679-352">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

::: moniker-end

::: moniker range="< aspnetcore-2.2"

# <a name="nettabdotnet"></a>[<span data-ttu-id="43679-353">.NET</span><span class="sxs-lookup"><span data-stu-id="43679-353">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="43679-354">옵션</span><span class="sxs-lookup"><span data-stu-id="43679-354">Option</span></span> | <span data-ttu-id="43679-355">기본값</span><span class="sxs-lookup"><span data-stu-id="43679-355">Default value</span></span> | <span data-ttu-id="43679-356">설명</span><span class="sxs-lookup"><span data-stu-id="43679-356">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="43679-357">30 초 (3만 밀리초)</span><span class="sxs-lookup"><span data-stu-id="43679-357">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="43679-358">서버 작업에 대 한 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="43679-358">Timeout for server activity.</span></span> <span data-ttu-id="43679-359">서버가이 간격 내에 메시지를 보내지 않은 경우 클라이언트는 서버 연결을 끊고 `Closed` 이벤트 (JavaScript의`onclose`)를 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="43679-359">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="43679-360">이 값은 ping 메시지 **를 서버에서 보내고 시간** 제한 간격 내에 클라이언트에서 받을 수 있도록 충분히 커야 합니다.</span><span class="sxs-lookup"><span data-stu-id="43679-360">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="43679-361">권장 값은 ping이 도착할 때까지 시간을 허용 하기 위해 서버의 `KeepAliveInterval` 값을 두 번 이상 사용 하는 숫자입니다.</span><span class="sxs-lookup"><span data-stu-id="43679-361">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="43679-362">15초</span><span class="sxs-lookup"><span data-stu-id="43679-362">15 seconds</span></span> | <span data-ttu-id="43679-363">초기 서버 핸드셰이크에 대 한 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="43679-363">Timeout for initial server handshake.</span></span> <span data-ttu-id="43679-364">서버에서이 간격으로 핸드셰이크 응답을 보내지 않는 경우 클라이언트는 핸드셰이크를 취소 하 고 `Closed` 이벤트 (JavaScript의`onclose`)를 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="43679-364">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="43679-365">심각한 네트워크 대기 시간으로 인해 핸드셰이크 시간 초과 오류가 발생 하는 경우에만 수정 해야 하는 고급 설정입니다.</span><span class="sxs-lookup"><span data-stu-id="43679-365">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="43679-366">핸드셰이크 프로세스에 대 한 자세한 내용은 [SignalR 허브 프로토콜 사양](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="43679-366">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

<span data-ttu-id="43679-367">.NET 클라이언트에서 시간 제한 값은 `TimeSpan` 값으로 지정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="43679-367">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascripttabjavascript"></a>[<span data-ttu-id="43679-368">JavaScript</span><span class="sxs-lookup"><span data-stu-id="43679-368">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="43679-369">옵션</span><span class="sxs-lookup"><span data-stu-id="43679-369">Option</span></span> | <span data-ttu-id="43679-370">기본값</span><span class="sxs-lookup"><span data-stu-id="43679-370">Default value</span></span> | <span data-ttu-id="43679-371">설명</span><span class="sxs-lookup"><span data-stu-id="43679-371">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="43679-372">30 초 (3만 밀리초)</span><span class="sxs-lookup"><span data-stu-id="43679-372">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="43679-373">서버 작업에 대 한 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="43679-373">Timeout for server activity.</span></span> <span data-ttu-id="43679-374">서버가이 간격 내에 메시지를 보내지 않은 경우 클라이언트는 서버 연결을 끊고 `onclose` 이벤트를 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="43679-374">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="43679-375">이 값은 ping 메시지 **를 서버에서 보내고 시간** 제한 간격 내에 클라이언트에서 받을 수 있도록 충분히 커야 합니다.</span><span class="sxs-lookup"><span data-stu-id="43679-375">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="43679-376">권장 값은 ping이 도착할 때까지 시간을 허용 하기 위해 서버의 `KeepAliveInterval` 값을 두 번 이상 사용 하는 숫자입니다.</span><span class="sxs-lookup"><span data-stu-id="43679-376">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |

# <a name="javatabjava"></a>[<span data-ttu-id="43679-377">Java</span><span class="sxs-lookup"><span data-stu-id="43679-377">Java</span></span>](#tab/java)

| <span data-ttu-id="43679-378">옵션</span><span class="sxs-lookup"><span data-stu-id="43679-378">Option</span></span> | <span data-ttu-id="43679-379">기본값</span><span class="sxs-lookup"><span data-stu-id="43679-379">Default value</span></span> | <span data-ttu-id="43679-380">설명</span><span class="sxs-lookup"><span data-stu-id="43679-380">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="43679-381">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="43679-381">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="43679-382">30 초 (3만 밀리초)</span><span class="sxs-lookup"><span data-stu-id="43679-382">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="43679-383">서버 작업에 대 한 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="43679-383">Timeout for server activity.</span></span> <span data-ttu-id="43679-384">서버가이 간격 내에 메시지를 보내지 않은 경우 클라이언트는 서버 연결을 끊고 `onClose` 이벤트를 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="43679-384">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="43679-385">이 값은 ping 메시지 **를 서버에서 보내고 시간** 제한 간격 내에 클라이언트에서 받을 수 있도록 충분히 커야 합니다.</span><span class="sxs-lookup"><span data-stu-id="43679-385">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="43679-386">권장 값은 ping이 도착할 시간을 허용 하기 위해 서버의 `KeepAliveInterval` 값을 두 번 이상 사용 하는 숫자입니다.</span><span class="sxs-lookup"><span data-stu-id="43679-386">The recommended value is a number at least double the server's `KeepAliveInterval` value, to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="43679-387">15초</span><span class="sxs-lookup"><span data-stu-id="43679-387">15 seconds</span></span> | <span data-ttu-id="43679-388">초기 서버 핸드셰이크에 대 한 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="43679-388">Timeout for initial server handshake.</span></span> <span data-ttu-id="43679-389">서버가이 간격 내에 핸드셰이크 응답을 보내지 않는 경우 클라이언트는 핸드셰이크를 취소 하 고 `onClose` 이벤트를 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="43679-389">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="43679-390">심각한 네트워크 대기 시간으로 인해 핸드셰이크 시간 초과 오류가 발생 하는 경우에만 수정 해야 하는 고급 설정입니다.</span><span class="sxs-lookup"><span data-stu-id="43679-390">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="43679-391">핸드셰이크 프로세스에 대 한 자세한 내용은 [SignalR 허브 프로토콜 사양](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="43679-391">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

::: moniker-end

---

### <a name="configure-additional-options"></a><span data-ttu-id="43679-392">추가 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="43679-392">Configure additional options</span></span>

<span data-ttu-id="43679-393">추가 옵션은 Java 클라이언트의 `HttpHubConnectionBuilder`에서 `HubConnectionBuilder` 또는 다양 한 구성 Api에 대 한 `WithUrl` (JavaScript의`withUrl`) 방법으로 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="43679-393">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="nettabdotnet"></a>[<span data-ttu-id="43679-394">.NET</span><span class="sxs-lookup"><span data-stu-id="43679-394">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="43679-395">.NET 옵션</span><span class="sxs-lookup"><span data-stu-id="43679-395">.NET Option</span></span> |  <span data-ttu-id="43679-396">기본값</span><span class="sxs-lookup"><span data-stu-id="43679-396">Default value</span></span> | <span data-ttu-id="43679-397">설명</span><span class="sxs-lookup"><span data-stu-id="43679-397">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="43679-398">HTTP 요청에서 전달자 인증 토큰으로 제공 되는 문자열을 반환 하는 함수입니다.</span><span class="sxs-lookup"><span data-stu-id="43679-398">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="43679-399">협상 단계를 건너뛰려면 `true`로 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="43679-399">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="43679-400">**Websocket 전송이 유일 하 게 설정 된 전송 인 경우에만 지원 됩니다**.</span><span class="sxs-lookup"><span data-stu-id="43679-400">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="43679-401">Azure SignalR 서비스를 사용 하는 경우이 설정을 사용 하도록 설정할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="43679-401">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="43679-402">Empty</span><span class="sxs-lookup"><span data-stu-id="43679-402">Empty</span></span> | <span data-ttu-id="43679-403">인증 요청에 보낼 TLS 인증서 컬렉션입니다.</span><span class="sxs-lookup"><span data-stu-id="43679-403">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="43679-404">Empty</span><span class="sxs-lookup"><span data-stu-id="43679-404">Empty</span></span> | <span data-ttu-id="43679-405">모든 HTTP 요청과 함께 보낼 HTTP 쿠키의 컬렉션입니다.</span><span class="sxs-lookup"><span data-stu-id="43679-405">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="43679-406">Empty</span><span class="sxs-lookup"><span data-stu-id="43679-406">Empty</span></span> | <span data-ttu-id="43679-407">모든 HTTP 요청과 함께 전송할 자격 증명입니다.</span><span class="sxs-lookup"><span data-stu-id="43679-407">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="43679-408">5초</span><span class="sxs-lookup"><span data-stu-id="43679-408">5 seconds</span></span> | <span data-ttu-id="43679-409">Websocket 전용입니다.</span><span class="sxs-lookup"><span data-stu-id="43679-409">WebSockets only.</span></span> <span data-ttu-id="43679-410">서버가 닫기 요청을 승인할 때까지 대기한 후 클라이언트에서 대기 하는 최대 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="43679-410">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="43679-411">이 시간 내에 서버에서 닫기를 승인 하지 않으면 클라이언트 연결이 끊어집니다.</span><span class="sxs-lookup"><span data-stu-id="43679-411">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="43679-412">Empty</span><span class="sxs-lookup"><span data-stu-id="43679-412">Empty</span></span> | <span data-ttu-id="43679-413">모든 HTTP 요청과 함께 보낼 추가 HTTP 헤더의 맵입니다.</span><span class="sxs-lookup"><span data-stu-id="43679-413">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="43679-414">HTTP 요청을 보내는 데 사용 되는 `HttpMessageHandler`를 구성 하거나 대체 하는 데 사용할 수 있는 대리자입니다.</span><span class="sxs-lookup"><span data-stu-id="43679-414">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="43679-415">WebSocket 연결에는 사용 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="43679-415">Not used for WebSocket connections.</span></span> <span data-ttu-id="43679-416">이 대리자는 null이 아닌 값을 반환 해야 하며 기본값을 매개 변수로 받습니다.</span><span class="sxs-lookup"><span data-stu-id="43679-416">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="43679-417">해당 기본값에 대 한 설정을 수정 하 고 반환 하거나 새 `HttpMessageHandler` 인스턴스를 반환 합니다.</span><span class="sxs-lookup"><span data-stu-id="43679-417">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="43679-418">**처리기를 대체할 때 제공 된 처리기에서 유지할 설정을 복사 해야 합니다. 그렇지 않으면 구성 된 옵션 (예: 쿠키 및 헤더)이 새 처리기에 적용 되지 않습니다.**</span><span class="sxs-lookup"><span data-stu-id="43679-418">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="43679-419">HTTP 요청을 보낼 때 사용할 HTTP 프록시입니다.</span><span class="sxs-lookup"><span data-stu-id="43679-419">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="43679-420">HTTP 및 Websocket 요청에 대 한 기본 자격 증명을 보내려면이 부울 값을 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="43679-420">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="43679-421">이렇게 하면 Windows 인증을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="43679-421">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="43679-422">추가 WebSocket 옵션을 구성 하는 데 사용할 수 있는 대리자입니다.</span><span class="sxs-lookup"><span data-stu-id="43679-422">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="43679-423">옵션을 구성 하는 데 사용할 수 있는 [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) 의 인스턴스를 수신 합니다.</span><span class="sxs-lookup"><span data-stu-id="43679-423">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascripttabjavascript"></a>[<span data-ttu-id="43679-424">JavaScript</span><span class="sxs-lookup"><span data-stu-id="43679-424">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="43679-425">JavaScript 옵션</span><span class="sxs-lookup"><span data-stu-id="43679-425">JavaScript Option</span></span> | <span data-ttu-id="43679-426">기본값</span><span class="sxs-lookup"><span data-stu-id="43679-426">Default Value</span></span> | <span data-ttu-id="43679-427">설명</span><span class="sxs-lookup"><span data-stu-id="43679-427">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="43679-428">HTTP 요청에서 전달자 인증 토큰으로 제공 되는 문자열을 반환 하는 함수입니다.</span><span class="sxs-lookup"><span data-stu-id="43679-428">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="43679-429">협상 단계를 건너뛰려면 `true`로 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="43679-429">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="43679-430">**Websocket 전송이 유일 하 게 설정 된 전송 인 경우에만 지원 됩니다**.</span><span class="sxs-lookup"><span data-stu-id="43679-430">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="43679-431">Azure SignalR 서비스를 사용 하는 경우이 설정을 사용 하도록 설정할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="43679-431">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="javatabjava"></a>[<span data-ttu-id="43679-432">Java</span><span class="sxs-lookup"><span data-stu-id="43679-432">Java</span></span>](#tab/java)

| <span data-ttu-id="43679-433">Java 옵션</span><span class="sxs-lookup"><span data-stu-id="43679-433">Java Option</span></span> | <span data-ttu-id="43679-434">기본값</span><span class="sxs-lookup"><span data-stu-id="43679-434">Default Value</span></span> | <span data-ttu-id="43679-435">설명</span><span class="sxs-lookup"><span data-stu-id="43679-435">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="43679-436">HTTP 요청에서 전달자 인증 토큰으로 제공 되는 문자열을 반환 하는 함수입니다.</span><span class="sxs-lookup"><span data-stu-id="43679-436">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="43679-437">협상 단계를 건너뛰려면 `true`로 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="43679-437">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="43679-438">**Websocket 전송이 유일 하 게 설정 된 전송 인 경우에만 지원 됩니다**.</span><span class="sxs-lookup"><span data-stu-id="43679-438">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="43679-439">Azure SignalR 서비스를 사용 하는 경우이 설정을 사용 하도록 설정할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="43679-439">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="43679-440">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="43679-440">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="43679-441">Empty</span><span class="sxs-lookup"><span data-stu-id="43679-441">Empty</span></span> | <span data-ttu-id="43679-442">모든 HTTP 요청과 함께 보낼 추가 HTTP 헤더의 맵입니다.</span><span class="sxs-lookup"><span data-stu-id="43679-442">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="43679-443">.NET 클라이언트에서 이러한 옵션은 `WithUrl`에 제공 된 옵션 대리자로 수정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="43679-443">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="43679-444">JavaScript 클라이언트에서 이러한 옵션은 `withUrl`에 제공 된 JavaScript 개체에 제공 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="43679-444">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="43679-445">Java 클라이언트에서 이러한 옵션은에서 반환 되는 `HttpHubConnectionBuilder`에 대 한 메서드로 구성할 수 있습니다 `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="43679-445">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="43679-446">추가 자료</span><span class="sxs-lookup"><span data-stu-id="43679-446">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>
