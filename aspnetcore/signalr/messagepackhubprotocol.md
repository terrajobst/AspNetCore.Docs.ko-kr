---
title: ASP.NET Core SignalR에서 MessagePack 허브 프로토콜 사용
author: bradygaster
description: ASP.NET Core SignalR에 MessagePack 허브 프로토콜을 추가합니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 02/27/2019
uid: signalr/messagepackhubprotocol
ms.openlocfilehash: 7742f6f8bb53fb3c299ff98ae52a0da519ff396c
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64896520"
---
# <a name="use-messagepack-hub-protocol-in-signalr-for-aspnet-core"></a><span data-ttu-id="73a46-103">ASP.NET Core SignalR에서 MessagePack 허브 프로토콜 사용</span><span class="sxs-lookup"><span data-stu-id="73a46-103">Use MessagePack Hub Protocol in SignalR for ASP.NET Core</span></span>

<span data-ttu-id="73a46-104">작성자: [Brennan Conroy](https://github.com/BrennanConroy)</span><span class="sxs-lookup"><span data-stu-id="73a46-104">By [Brennan Conroy](https://github.com/BrennanConroy)</span></span>

<span data-ttu-id="73a46-105">이 문서는 독자가 [시작하기](xref:tutorials/signalr)에서 설명하는 내용을 잘 알고 있다고 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="73a46-105">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="73a46-106">MessagePack이란?</span><span class="sxs-lookup"><span data-stu-id="73a46-106">What is MessagePack?</span></span>

<span data-ttu-id="73a46-107">[MessagePack](https://msgpack.org/index.html)은 빠르고 간결한 이진 직렬화 포맷입니다.</span><span class="sxs-lookup"><span data-stu-id="73a46-107">[MessagePack](https://msgpack.org/index.html) is a binary serialization format that is fast and compact.</span></span> <span data-ttu-id="73a46-108">[JSON](https://www.json.org/)에 비해 크기가 작은 메시지를 생성하기 때문에 성능 및 대역폭이 중요한 경우에 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="73a46-108">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="73a46-109">이진 형식이므로 바이트가 MessagePack 파서를 거치지 않는 한 네트워크 추적 및 로그를 살펴보더라도 메시지를 읽을 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="73a46-109">Because it's a binary format, messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> <span data-ttu-id="73a46-110">SignalR은 MessagePack 형식을 기본으로 지원하며 클라이언트 및 서버에서 사용하기 위한 API를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="73a46-110">SignalR has built-in support for the MessagePack format, and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="73a46-111">서버에서 MessagePack 구성</span><span class="sxs-lookup"><span data-stu-id="73a46-111">Configure MessagePack on the server</span></span>

<span data-ttu-id="73a46-112">서버에서 MessagePack 허브 프로토콜을 사용하려면 앱에 `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` 패키지를 설치해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="73a46-112">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="73a46-113">Startup.cs 파일에서 `AddSignalR` 호출에 `AddMessagePackProtocol`을 추가하여 서버에서 MessagePack 지원을 활성화시킵니다.</span><span class="sxs-lookup"><span data-stu-id="73a46-113">In the Startup.cs file add `AddMessagePackProtocol` to the `AddSignalR` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="73a46-114">JSON은 기본적으로 활성화됩니다.</span><span class="sxs-lookup"><span data-stu-id="73a46-114">JSON is enabled by default.</span></span> <span data-ttu-id="73a46-115">MessagePack을 추가하면 JSON 및 MessagePack 클라이언트에 대한 기능이 모두 활성화됩니다.</span><span class="sxs-lookup"><span data-stu-id="73a46-115">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

<span data-ttu-id="73a46-116">MessagePack이 데이터를 서식화하는 방법을 사용자 지정하려면 `AddMessagePackProtocol`에 구성 옵션을 위한 대리자를 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="73a46-116">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="73a46-117">이 대리자에서 `FormatterResolvers` 속성을 이용하여 MessagePack 직렬화 옵션을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="73a46-117">In that delegate, the `FormatterResolvers` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="73a46-118">리졸버가 동작하는 방식에 대한 자세한 내용은 [MessagePack CSharp](https://github.com/neuecc/MessagePack-CSharp)의 MessagePack 라이브러리를 방문해보시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="73a46-118">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="73a46-119">직렬화하고자 하는 개체에 특성을 적용하여 해당 개체를 처리하는 방식을 정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="73a46-119">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

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

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="73a46-120">클라이언트에서 MessagePack 구성</span><span class="sxs-lookup"><span data-stu-id="73a46-120">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="73a46-121">JSON은 지원되는 클라이언트에 대해 기본적으로 활성화됩니다.</span><span class="sxs-lookup"><span data-stu-id="73a46-121">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="73a46-122">클라이언트는 단일 프로토콜만 지원할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="73a46-122">Clients can only support a single protocol.</span></span> <span data-ttu-id="73a46-123">MessagePack 지원을 추가하면 기존에 구성된 모든 프로토콜이 대체됩니다.</span><span class="sxs-lookup"><span data-stu-id="73a46-123">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="73a46-124">.NET 클라이언트</span><span class="sxs-lookup"><span data-stu-id="73a46-124">.NET client</span></span>

<span data-ttu-id="73a46-125">.NET 클라이언트에서 MessagePack을 활성화시키려면 `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` 패키지를 설치하고 `HubConnectionBuilder`에서 `AddMessagePackProtocol`을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="73a46-125">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chatHub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="73a46-126">서버와 마찬가지로 이 `AddMessagePackProtocol` 호출은 옵션을 구성하기 위한 대리자를 전달받습니다.</span><span class="sxs-lookup"><span data-stu-id="73a46-126">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="73a46-127">JavaScript 클라이언트</span><span class="sxs-lookup"><span data-stu-id="73a46-127">JavaScript client</span></span>

<span data-ttu-id="73a46-128">JavaScript 클라이언트에 대 한 지원은 MessagePack가 제공 된 `@aspnet/signalr-protocol-msgpack` npm 패키지 합니다.</span><span class="sxs-lookup"><span data-stu-id="73a46-128">MessagePack support for the JavaScript client is provided by the `@aspnet/signalr-protocol-msgpack` npm package.</span></span>

```console
npm install @aspnet/signalr-protocol-msgpack
```

<span data-ttu-id="73a46-129">npm 패키지를 설치한 뒤에 모듈을 JavaScript 모듈 로더를 통해서 직접 사용할 수도 있고 *node_modules\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js* 파일을 참조해서 브라우저로 가져올 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="73a46-129">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the *node_modules\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js* file.</span></span> <span data-ttu-id="73a46-130">브라우저에서 `msgpack5` 라이브러리도 참조해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="73a46-130">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="73a46-131">사용 된 `<script>` 대 한 참조를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="73a46-131">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="73a46-132">이 라이브러리는 *node_modules\msgpack5\dist\msgpack5.js*에서 찾을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="73a46-132">The library can be found at *node_modules\msgpack5\dist\msgpack5.js*.</span></span>

> [!NOTE]
> <span data-ttu-id="73a46-133">`<script>` 요소를 사용할 때 순서가 중요합니다.</span><span class="sxs-lookup"><span data-stu-id="73a46-133">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="73a46-134">*msgpack5.js*보다 *signalr-protocol-msgpack.js*를 먼저 참조하면 MessagePack을 이용해서 연결하려고 시도할 때 오류가 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="73a46-134">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js*, an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="73a46-135">*signalr.js*도 *signalr-protocol-msgpack.js*보다 먼저 참조해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="73a46-135">*signalr.js* is also required before *signalr-protocol-msgpack.js*.</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="73a46-136">`HubConnectionBuilder`에 `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())`을 추가하면 클라이언트가 서버에 연결할 때 MessagePack 프로토콜을 사용하도록 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="73a46-136">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="73a46-137">현재 JavaScript 클라이언트에는 MessagePack 프로토콜에 대한 구성 옵션이 존재하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="73a46-137">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

## <a name="messagepack-quirks"></a><span data-ttu-id="73a46-138">MessagePack 쿼크</span><span class="sxs-lookup"><span data-stu-id="73a46-138">MessagePack quirks</span></span>

<span data-ttu-id="73a46-139">MessagePack Hub 프로토콜을 사용 하는 경우 알아야 할 몇 가지 문제가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="73a46-139">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="73a46-140">MessagePack는 대/소문자 구분</span><span class="sxs-lookup"><span data-stu-id="73a46-140">MessagePack is case-sensitive</span></span>

<span data-ttu-id="73a46-141">MessagePack 프로토콜은 대/소문자 구분입니다.</span><span class="sxs-lookup"><span data-stu-id="73a46-141">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="73a46-142">예를 들어 다음 C# 클래스:</span><span class="sxs-lookup"><span data-stu-id="73a46-142">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="73a46-143">JavaScript 클라이언트에서 전송 하는 경우에 사용 해야 `PascalCased` 속성 이름은 대/소문자와 일치 해야 합니다는 C# 클래스를 정확 하 게 합니다.</span><span class="sxs-lookup"><span data-stu-id="73a46-143">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="73a46-144">예를 들어:</span><span class="sxs-lookup"><span data-stu-id="73a46-144">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="73a46-145">사용 하 여 `camelCased` 이름을 않습니다 바인딩할 합니다 C# 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="73a46-145">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="73a46-146">사용 하 여이 문제를 해결 해도 `Key` MessagePack 속성에 대해 다른 이름을 지정 하는 특성입니다.</span><span class="sxs-lookup"><span data-stu-id="73a46-146">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="73a46-147">자세한 내용은 [MessagePack CSharp 설명서](https://github.com/neuecc/MessagePack-CSharp#object-serialization)합니다.</span><span class="sxs-lookup"><span data-stu-id="73a46-147">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="73a46-148">직렬화/역직렬화 DateTime.Kind 유지 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="73a46-148">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="73a46-149">MessagePack 프로토콜을 인코딩하는 방법을 제공 하지 않습니다 합니다 `Kind` 의 값을 `DateTime`입니다.</span><span class="sxs-lookup"><span data-stu-id="73a46-149">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="73a46-150">결과적으로 날짜를 역직렬화 하는 동안, MessagePack 허브 프로토콜 들어오는 날짜가 UTC 형식으로 가정 합니다.</span><span class="sxs-lookup"><span data-stu-id="73a46-150">As a result, when deserializing a date, the MessagePack Hub Protocol assumes the incoming date is in UTC format.</span></span> <span data-ttu-id="73a46-151">사용 하는 경우 `DateTime` 현지 시간 값을 권장 보내기 전에 UTC로 변환 합니다.</span><span class="sxs-lookup"><span data-stu-id="73a46-151">If you're working with `DateTime` values in local time, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="73a46-152">변환할 UTC에서 현지 시간을 받을 때마다 합니다.</span><span class="sxs-lookup"><span data-stu-id="73a46-152">Convert them from UTC to local time when you receive them.</span></span>

<span data-ttu-id="73a46-153">이 제한에 대 한 자세한 내용은 GitHub을 참조 하세요. 문제가 [aspnet/SignalR #2632](https://github.com/aspnet/SignalR/issues/2632)합니다.</span><span class="sxs-lookup"><span data-stu-id="73a46-153">For more information on this limitation, see GitHub issue [aspnet/SignalR#2632](https://github.com/aspnet/SignalR/issues/2632).</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="73a46-154">DateTime.MinValue는 MessagePack javascript에서에서 지원 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="73a46-154">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="73a46-155">합니다 [msgpack5](https://github.com/mcollina/msgpack5) SignalR JavaScript 클라이언트에서 사용 되는 라이브러리를 지원 하지 않습니다는 `timestamp96` MessagePack의 형식입니다.</span><span class="sxs-lookup"><span data-stu-id="73a46-155">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the SignalR JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="73a46-156">이 형식은 매우 큰 날짜 값 (또는 과거의 초기에 나중에 매우 많이)를 인코딩하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="73a46-156">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="73a46-157">변수의 `DateTime.MinValue` 됩니다 `January 1, 0001` 는 인코딩해야는 `timestamp96` 값입니다.</span><span class="sxs-lookup"><span data-stu-id="73a46-157">The value of `DateTime.MinValue` is `January 1, 0001` which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="73a46-158">이 전송으로 인해 `DateTime.MinValue` 클라이언트 JavaScript에 지원 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="73a46-158">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="73a46-159">때 `DateTime.MinValue` 수신, JavaScript 클라이언트에서 다음 오류가 throw 됩니다.</span><span class="sxs-lookup"><span data-stu-id="73a46-159">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="73a46-160">일반적으로 `DateTime.MinValue` 인코딩하는 데 사용 되는 "missing" 또는 `null` 값입니다.</span><span class="sxs-lookup"><span data-stu-id="73a46-160">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="73a46-161">MessagePack에서 해당 값을 인코드 해야 할 경우 사용 하 여 null을 허용 `DateTime` 값 (`DateTime?`) 또는 별도 인코딩 `bool` 날짜 있는지를 나타내는 값입니다.</span><span class="sxs-lookup"><span data-stu-id="73a46-161">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="73a46-162">이 제한에 대 한 자세한 내용은 GitHub을 참조 하세요. 문제가 [aspnet/SignalR #2228](https://github.com/aspnet/SignalR/issues/2228)합니다.</span><span class="sxs-lookup"><span data-stu-id="73a46-162">For more information on this limitation, see GitHub issue [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="73a46-163">"미리-" 컴파일 환경에서 MessagePack 지원</span><span class="sxs-lookup"><span data-stu-id="73a46-163">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="73a46-164">합니다 [MessagePack CSharp](https://github.com/neuecc/MessagePack-CSharp) 라이브러리.NET 클라이언트 및 서버에서 사용 하는 serialization을 최적화 하기 위해 코드 생성을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="73a46-164">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="73a46-165">결과적으로, "미리-" 컴파일 (예: Xamarin iOS 또는 Unity)를 사용 하는 환경에서 기본적으로 지원 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="73a46-165">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="73a46-166">"미리 코드를 생성할" serializer/deserializer에서 MessagePack 이러한 환경에서 사용 하는 것이 가능 합니다.</span><span class="sxs-lookup"><span data-stu-id="73a46-166">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="73a46-167">자세한 내용은 [MessagePack CSharp 설명서](https://github.com/neuecc/MessagePack-CSharp#pre-code-generationunityxamarin-supports)합니다.</span><span class="sxs-lookup"><span data-stu-id="73a46-167">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#pre-code-generationunityxamarin-supports).</span></span> <span data-ttu-id="73a46-168">직렬 변환기를 미리 생성 한 후에 전달 된 구성 대리자를 사용 하 여 등록할 수 있습니다 `AddMessagePackProtocol`:</span><span class="sxs-lookup"><span data-stu-id="73a46-168">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

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

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="73a46-169">형식 검사는 더 엄격 하 게 MessagePack</span><span class="sxs-lookup"><span data-stu-id="73a46-169">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="73a46-170">JSON Hub 프로토콜을 역직렬화 하는 동안 형식 변환을 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="73a46-170">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="73a46-171">예를 들어 들어오는 개체 속성 값이 있는 숫자 인지 (`{ foo: 42 }`) 형식의.NET 클래스의 속성 이지만 `string`, 값 변환 됩니다.</span><span class="sxs-lookup"><span data-stu-id="73a46-171">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="73a46-172">그러나 MessagePack이이 변환을 수행 하지 않습니다 하 고 서버 쪽 로그 (및 콘솔)에서 볼 수 있는 예외를 throw 합니다.</span><span class="sxs-lookup"><span data-stu-id="73a46-172">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="73a46-173">이 제한에 대 한 자세한 내용은 GitHub을 참조 하세요. 문제가 [aspnet/SignalR #2937](https://github.com/aspnet/SignalR/issues/2937)합니다.</span><span class="sxs-lookup"><span data-stu-id="73a46-173">For more information on this limitation, see GitHub issue [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span></span>

## <a name="related-resources"></a><span data-ttu-id="73a46-174">관련 참고 자료</span><span class="sxs-lookup"><span data-stu-id="73a46-174">Related resources</span></span>

* [<span data-ttu-id="73a46-175">시작</span><span class="sxs-lookup"><span data-stu-id="73a46-175">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="73a46-176">.NET 클라이언트</span><span class="sxs-lookup"><span data-stu-id="73a46-176">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="73a46-177">JavaScript 클라이언트</span><span class="sxs-lookup"><span data-stu-id="73a46-177">JavaScript client</span></span>](xref:signalr/javascript-client)
