---
title: ASP.NET Core SignalR에서 MessagePack 허브 프로토콜 사용
author: bradygaster
description: ASP.NET Core SignalR에 MessagePack 허브 프로토콜을 추가합니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 10/08/2019
uid: signalr/messagepackhubprotocol
ms.openlocfilehash: fe09b646eba5ae15cbd9e568b276aaf7763e4b1b
ms.sourcegitcommit: fcdf9aaa6c45c1a926bd870ed8f893bdb4935152
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2019
ms.locfileid: "72165401"
---
# <a name="use-messagepack-hub-protocol-in-signalr-for-aspnet-core"></a><span data-ttu-id="1cb6d-103">ASP.NET Core SignalR에서 MessagePack 허브 프로토콜 사용</span><span class="sxs-lookup"><span data-stu-id="1cb6d-103">Use MessagePack Hub Protocol in SignalR for ASP.NET Core</span></span>

<span data-ttu-id="1cb6d-104">작성자: [Brennan Conroy](https://github.com/BrennanConroy)</span><span class="sxs-lookup"><span data-stu-id="1cb6d-104">By [Brennan Conroy](https://github.com/BrennanConroy)</span></span>

<span data-ttu-id="1cb6d-105">이 문서는 독자가 [시작하기](xref:tutorials/signalr)에서 설명하는 내용을 잘 알고 있다고 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="1cb6d-105">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="1cb6d-106">MessagePack이란?</span><span class="sxs-lookup"><span data-stu-id="1cb6d-106">What is MessagePack?</span></span>

<span data-ttu-id="1cb6d-107">[MessagePack](https://msgpack.org/index.html)은 빠르고 간결한 이진 직렬화 포맷입니다.</span><span class="sxs-lookup"><span data-stu-id="1cb6d-107">[MessagePack](https://msgpack.org/index.html) is a binary serialization format that is fast and compact.</span></span> <span data-ttu-id="1cb6d-108">[JSON](https://www.json.org/)에 비해 크기가 작은 메시지를 생성하기 때문에 성능 및 대역폭이 중요한 경우에 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="1cb6d-108">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="1cb6d-109">이진 형식이므로 바이트가 MessagePack 파서를 거치지 않는 한 네트워크 추적 및 로그를 살펴보더라도 메시지를 읽을 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="1cb6d-109">Because it's a binary format, messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> <span data-ttu-id="1cb6d-110">SignalR은 MessagePack 형식을 기본으로 지원하며 클라이언트 및 서버에서 사용하기 위한 API를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="1cb6d-110">SignalR has built-in support for the MessagePack format, and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="1cb6d-111">서버에서 MessagePack 구성</span><span class="sxs-lookup"><span data-stu-id="1cb6d-111">Configure MessagePack on the server</span></span>

<span data-ttu-id="1cb6d-112">서버에서 MessagePack 허브 프로토콜을 사용하려면 앱에 `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` 패키지를 설치해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="1cb6d-112">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="1cb6d-113">Startup.cs 파일에서 `AddSignalR` 호출에 `AddMessagePackProtocol`을 추가하여 서버에서 MessagePack 지원을 활성화시킵니다.</span><span class="sxs-lookup"><span data-stu-id="1cb6d-113">In the Startup.cs file add `AddMessagePackProtocol` to the `AddSignalR` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="1cb6d-114">JSON은 기본적으로 활성화됩니다.</span><span class="sxs-lookup"><span data-stu-id="1cb6d-114">JSON is enabled by default.</span></span> <span data-ttu-id="1cb6d-115">MessagePack을 추가하면 JSON 및 MessagePack 클라이언트에 대한 기능이 모두 활성화됩니다.</span><span class="sxs-lookup"><span data-stu-id="1cb6d-115">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

<span data-ttu-id="1cb6d-116">MessagePack이 데이터를 서식화하는 방법을 사용자 지정하려면 `AddMessagePackProtocol`에 구성 옵션을 위한 대리자를 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="1cb6d-116">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="1cb6d-117">이 대리자에서 `FormatterResolvers` 속성을 이용하여 MessagePack 직렬화 옵션을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1cb6d-117">In that delegate, the `FormatterResolvers` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="1cb6d-118">리졸버가 동작하는 방식에 대한 자세한 내용은 [MessagePack CSharp](https://github.com/neuecc/MessagePack-CSharp)의 MessagePack 라이브러리를 방문해보시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="1cb6d-118">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="1cb6d-119">직렬화하고자 하는 개체에 특성을 적용하여 해당 개체를 처리하는 방식을 정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1cb6d-119">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol(options =>
    {
        options.FormatterResolvers = new List<MessagePack.IFormatterResolver>()
        {
            MessagePack.Resolvers.StandardResolver.Instance
        };
    });
```

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="1cb6d-120">클라이언트에서 MessagePack 구성</span><span class="sxs-lookup"><span data-stu-id="1cb6d-120">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="1cb6d-121">JSON은 지원되는 클라이언트에 대해 기본적으로 활성화됩니다.</span><span class="sxs-lookup"><span data-stu-id="1cb6d-121">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="1cb6d-122">클라이언트는 단일 프로토콜만 지원할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1cb6d-122">Clients can only support a single protocol.</span></span> <span data-ttu-id="1cb6d-123">MessagePack 지원을 추가하면 기존에 구성된 모든 프로토콜이 대체됩니다.</span><span class="sxs-lookup"><span data-stu-id="1cb6d-123">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="1cb6d-124">.NET 클라이언트</span><span class="sxs-lookup"><span data-stu-id="1cb6d-124">.NET client</span></span>

<span data-ttu-id="1cb6d-125">.NET 클라이언트에서 MessagePack을 활성화시키려면 `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` 패키지를 설치하고 `HubConnectionBuilder`에서 `AddMessagePackProtocol`을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="1cb6d-125">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chatHub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="1cb6d-126">서버와 마찬가지로 이 `AddMessagePackProtocol` 호출은 옵션을 구성하기 위한 대리자를 전달받습니다.</span><span class="sxs-lookup"><span data-stu-id="1cb6d-126">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="1cb6d-127">JavaScript 클라이언트</span><span class="sxs-lookup"><span data-stu-id="1cb6d-127">JavaScript client</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="1cb6d-128">JavaScript 클라이언트에 대 한 MessagePack 지원은 `@microsoft/signalr-protocol-msgpack` npm 패키지에서 제공 됩니다.</span><span class="sxs-lookup"><span data-stu-id="1cb6d-128">MessagePack support for the JavaScript client is provided by the `@microsoft/signalr-protocol-msgpack` npm package.</span></span> <span data-ttu-id="1cb6d-129">명령 셸에서 다음 명령을 실행 하 여 패키지를 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="1cb6d-129">Install the package by executing the following command in a command shell:</span></span>

```console
npm install @microsoft/signalr-protocol-msgpack
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="1cb6d-130">JavaScript 클라이언트에 대 한 MessagePack 지원은 `@aspnet/signalr-protocol-msgpack` npm 패키지에서 제공 됩니다.</span><span class="sxs-lookup"><span data-stu-id="1cb6d-130">MessagePack support for the JavaScript client is provided by the `@aspnet/signalr-protocol-msgpack` npm package.</span></span> <span data-ttu-id="1cb6d-131">명령 셸에서 다음 명령을 실행 하 여 패키지를 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="1cb6d-131">Install the package by executing the following command in a command shell:</span></span>

```console
npm install @aspnet/signalr-protocol-msgpack
```

::: moniker-end

<span data-ttu-id="1cb6d-132">Npm 패키지를 설치한 후 다음 파일을 참조 하 여 JavaScript 모듈 로더를 통해 직접 모듈을 사용 하거나 브라우저로 가져올 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1cb6d-132">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the following file:</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="1cb6d-133">*node_modules @ no__t-1 @ no__t*</span><span class="sxs-lookup"><span data-stu-id="1cb6d-133">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span> 

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="1cb6d-134">*node_modules @ no__t-1 @ no__t*</span><span class="sxs-lookup"><span data-stu-id="1cb6d-134">*node_modules\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span> 

::: moniker-end

<span data-ttu-id="1cb6d-135">브라우저에서 `msgpack5` 라이브러리도 참조해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="1cb6d-135">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="1cb6d-136">@No__t-0 태그를 사용 하 여 참조를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="1cb6d-136">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="1cb6d-137">이 라이브러리는 *node_modules\msgpack5\dist\msgpack5.js*에서 찾을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1cb6d-137">The library can be found at *node_modules\msgpack5\dist\msgpack5.js*.</span></span>

> [!NOTE]
> <span data-ttu-id="1cb6d-138">`<script>` 요소를 사용할 때 순서가 중요합니다.</span><span class="sxs-lookup"><span data-stu-id="1cb6d-138">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="1cb6d-139">*msgpack5.js*보다 *signalr-protocol-msgpack.js*를 먼저 참조하면 MessagePack을 이용해서 연결하려고 시도할 때 오류가 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="1cb6d-139">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js*, an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="1cb6d-140">*signalr.js*도 *signalr-protocol-msgpack.js*보다 먼저 참조해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="1cb6d-140">*signalr.js* is also required before *signalr-protocol-msgpack.js*.</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="1cb6d-141">`HubConnectionBuilder`에 `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())`을 추가하면 클라이언트가 서버에 연결할 때 MessagePack 프로토콜을 사용하도록 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="1cb6d-141">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="1cb6d-142">현재 JavaScript 클라이언트에는 MessagePack 프로토콜에 대한 구성 옵션이 존재하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="1cb6d-142">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

## <a name="messagepack-quirks"></a><span data-ttu-id="1cb6d-143">MessagePack 특수</span><span class="sxs-lookup"><span data-stu-id="1cb6d-143">MessagePack quirks</span></span>

<span data-ttu-id="1cb6d-144">MessagePack Hub 프로토콜을 사용할 때 알아야 할 몇 가지 문제가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1cb6d-144">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="1cb6d-145">MessagePack은 대/소문자를 구분 합니다.</span><span class="sxs-lookup"><span data-stu-id="1cb6d-145">MessagePack is case-sensitive</span></span>

<span data-ttu-id="1cb6d-146">MessagePack 프로토콜은 대/소문자를 구분 합니다.</span><span class="sxs-lookup"><span data-stu-id="1cb6d-146">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="1cb6d-147">예를 들어 다음 C# 클래스를 살펴보세요.</span><span class="sxs-lookup"><span data-stu-id="1cb6d-147">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="1cb6d-148">JavaScript 클라이언트에서 전송 하는 경우 대/소문자가 C# 클래스와 정확 하 게 일치 해야 하므로 `PascalCased` 속성 이름을 사용 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="1cb6d-148">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="1cb6d-149">예를 들어 다음과 같은 가치를 제공해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="1cb6d-149">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="1cb6d-150">@No__t 0을 사용 하면 C# 클래스에 올바르게 바인딩되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="1cb6d-150">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="1cb6d-151">@No__t-0 특성을 사용 하 여 MessagePack 속성에 다른 이름을 지정 하면이 문제를 해결할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1cb6d-151">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="1cb6d-152">자세한 내용은 [MessagePack-CSharp 설명서](https://github.com/neuecc/MessagePack-CSharp#object-serialization)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="1cb6d-152">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="1cb6d-153">Serialize/deserialize 할 때에는 DateTime이 유지 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="1cb6d-153">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="1cb6d-154">MessagePack 프로토콜은 `DateTime`의 `Kind` 값을 인코딩하는 방법을 제공 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="1cb6d-154">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="1cb6d-155">결과적으로, 날짜를 deserialize 할 때 MessagePack Hub 프로토콜은 들어오는 날짜가 UTC 형식인 것으로 가정 합니다.</span><span class="sxs-lookup"><span data-stu-id="1cb6d-155">As a result, when deserializing a date, the MessagePack Hub Protocol assumes the incoming date is in UTC format.</span></span> <span data-ttu-id="1cb6d-156">현지 시간에 `DateTime` 값을 사용 하 여 작업 하는 경우에는 값을 보내기 전에 UTC로 변환 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="1cb6d-156">If you're working with `DateTime` values in local time, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="1cb6d-157">받은 UTC에서 현지 시간으로 변환 합니다.</span><span class="sxs-lookup"><span data-stu-id="1cb6d-157">Convert them from UTC to local time when you receive them.</span></span>

<span data-ttu-id="1cb6d-158">이 제한 사항에 대 한 자세한 내용은 GitHub 문제 [aspnet/SignalR # 2632](https://github.com/aspnet/SignalR/issues/2632)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="1cb6d-158">For more information on this limitation, see GitHub issue [aspnet/SignalR#2632](https://github.com/aspnet/SignalR/issues/2632).</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="1cb6d-159">MinValue는 JavaScript의 MessagePack에서 지원 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="1cb6d-159">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="1cb6d-160">SignalR JavaScript 클라이언트에서 사용 하는 [msgpack5](https://github.com/mcollina/msgpack5) 라이브러리는 MessagePack에서 `timestamp96` 형식을 지원 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="1cb6d-160">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the SignalR JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="1cb6d-161">이 형식은 매우 큰 날짜 값을 인코딩하는 데 사용 됩니다 (이전에는 매우 일찍 또는 나중에 매우 오래 됨).</span><span class="sxs-lookup"><span data-stu-id="1cb6d-161">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="1cb6d-162">@No__t-0의 값은 `timestamp96` 값으로 인코딩해야 하는 `January 1, 0001`입니다.</span><span class="sxs-lookup"><span data-stu-id="1cb6d-162">The value of `DateTime.MinValue` is `January 1, 0001` which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="1cb6d-163">따라서 JavaScript 클라이언트에 `DateTime.MinValue`을 보내는 것은 지원 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="1cb6d-163">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="1cb6d-164">JavaScript 클라이언트에서 `DateTime.MinValue`을 받으면 다음 오류가 throw 됩니다.</span><span class="sxs-lookup"><span data-stu-id="1cb6d-164">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="1cb6d-165">일반적으로 `DateTime.MinValue`은 "missing" 또는 `null` 값을 인코딩하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="1cb6d-165">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="1cb6d-166">MessagePack에서 해당 값을 인코딩해야 하는 경우 nullable `DateTime` 값 (`DateTime?`)을 사용 하거나 날짜가 있는지를 나타내는 별도의 `bool` 값을 인코딩합니다.</span><span class="sxs-lookup"><span data-stu-id="1cb6d-166">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="1cb6d-167">이 제한 사항에 대 한 자세한 내용은 GitHub 문제 [aspnet/SignalR # 2228](https://github.com/aspnet/SignalR/issues/2228)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="1cb6d-167">For more information on this limitation, see GitHub issue [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="1cb6d-168">"사전" 컴파일 환경에서 MessagePack 지원</span><span class="sxs-lookup"><span data-stu-id="1cb6d-168">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="1cb6d-169">.NET 클라이언트 및 서버에서 사용 하는 [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp) 라이브러리는 코드 생성을 사용 하 여 serialization을 최적화 합니다.</span><span class="sxs-lookup"><span data-stu-id="1cb6d-169">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="1cb6d-170">따라서 "미리" 컴파일 (예: Xamarin iOS 또는 Unity)을 사용 하는 환경에서는 기본적으로 지원 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="1cb6d-170">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="1cb6d-171">직렬 변환기/역직렬 변환기 코드를 "미리 생성" 하 여 이러한 환경에서 MessagePack를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1cb6d-171">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="1cb6d-172">자세한 내용은 [MessagePack-CSharp 설명서](https://github.com/neuecc/MessagePack-CSharp#pre-code-generationunityxamarin-supports)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="1cb6d-172">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#pre-code-generationunityxamarin-supports).</span></span> <span data-ttu-id="1cb6d-173">Serializer를 미리 생성 한 후에는 `AddMessagePackProtocol`에 전달 된 구성 대리자를 사용 하 여 해당 serializer를 등록할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1cb6d-173">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol(options =>
    {
        options.FormatterResolvers = new List<MessagePack.IFormatterResolver>()
        {
            MessagePack.Resolvers.GeneratedResolver.Instance,
            MessagePack.Resolvers.StandardResolver.Instance
        };
    });
```

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="1cb6d-174">MessagePack에서 형식 검사가 더 엄격 합니다.</span><span class="sxs-lookup"><span data-stu-id="1cb6d-174">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="1cb6d-175">JSON 허브 프로토콜은 deserialization 중에 형식 변환을 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="1cb6d-175">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="1cb6d-176">예를 들어 들어오는 개체에 숫자 (`{ foo: 42 }`) 인 속성 값이 있지만 .NET 클래스의 속성이 `string` 형식인 경우 값이 변환 됩니다.</span><span class="sxs-lookup"><span data-stu-id="1cb6d-176">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="1cb6d-177">그러나 MessagePack는이 변환을 수행 하지 않으며 서버 쪽 로그 (및 콘솔)에서 볼 수 있는 예외를 throw 합니다.</span><span class="sxs-lookup"><span data-stu-id="1cb6d-177">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="1cb6d-178">이 제한 사항에 대 한 자세한 내용은 GitHub 문제 [aspnet/SignalR # 2937](https://github.com/aspnet/SignalR/issues/2937)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="1cb6d-178">For more information on this limitation, see GitHub issue [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span></span>

## <a name="related-resources"></a><span data-ttu-id="1cb6d-179">관련 자료</span><span class="sxs-lookup"><span data-stu-id="1cb6d-179">Related resources</span></span>

* [<span data-ttu-id="1cb6d-180">시작</span><span class="sxs-lookup"><span data-stu-id="1cb6d-180">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="1cb6d-181">.NET 클라이언트</span><span class="sxs-lookup"><span data-stu-id="1cb6d-181">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="1cb6d-182">JavaScript 클라이언트</span><span class="sxs-lookup"><span data-stu-id="1cb6d-182">JavaScript client</span></span>](xref:signalr/javascript-client)
