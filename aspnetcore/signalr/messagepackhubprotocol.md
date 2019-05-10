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
# <a name="use-messagepack-hub-protocol-in-signalr-for-aspnet-core"></a>ASP.NET Core SignalR에서 MessagePack 허브 프로토콜 사용

작성자: [Brennan Conroy](https://github.com/BrennanConroy)

이 문서는 독자가 [시작하기](xref:tutorials/signalr)에서 설명하는 내용을 잘 알고 있다고 가정합니다.

## <a name="what-is-messagepack"></a>MessagePack이란?

[MessagePack](https://msgpack.org/index.html)은 빠르고 간결한 이진 직렬화 포맷입니다. [JSON](https://www.json.org/)에 비해 크기가 작은 메시지를 생성하기 때문에 성능 및 대역폭이 중요한 경우에 유용합니다. 이진 형식이므로 바이트가 MessagePack 파서를 거치지 않는 한 네트워크 추적 및 로그를 살펴보더라도 메시지를 읽을 수 없습니다. SignalR은 MessagePack 형식을 기본으로 지원하며 클라이언트 및 서버에서 사용하기 위한 API를 제공합니다.

## <a name="configure-messagepack-on-the-server"></a>서버에서 MessagePack 구성

서버에서 MessagePack 허브 프로토콜을 사용하려면 앱에 `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` 패키지를 설치해야 합니다. Startup.cs 파일에서 `AddSignalR` 호출에 `AddMessagePackProtocol`을 추가하여 서버에서 MessagePack 지원을 활성화시킵니다.

> [!NOTE]
> JSON은 기본적으로 활성화됩니다. MessagePack을 추가하면 JSON 및 MessagePack 클라이언트에 대한 기능이 모두 활성화됩니다.

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

MessagePack이 데이터를 서식화하는 방법을 사용자 지정하려면 `AddMessagePackProtocol`에 구성 옵션을 위한 대리자를 전달합니다. 이 대리자에서 `FormatterResolvers` 속성을 이용하여 MessagePack 직렬화 옵션을 구성할 수 있습니다. 리졸버가 동작하는 방식에 대한 자세한 내용은 [MessagePack CSharp](https://github.com/neuecc/MessagePack-CSharp)의 MessagePack 라이브러리를 방문해보시기 바랍니다. 직렬화하고자 하는 개체에 특성을 적용하여 해당 개체를 처리하는 방식을 정의할 수 있습니다.

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

## <a name="configure-messagepack-on-the-client"></a>클라이언트에서 MessagePack 구성

> [!NOTE]
> JSON은 지원되는 클라이언트에 대해 기본적으로 활성화됩니다. 클라이언트는 단일 프로토콜만 지원할 수 있습니다. MessagePack 지원을 추가하면 기존에 구성된 모든 프로토콜이 대체됩니다.

### <a name="net-client"></a>.NET 클라이언트

.NET 클라이언트에서 MessagePack을 활성화시키려면 `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` 패키지를 설치하고 `HubConnectionBuilder`에서 `AddMessagePackProtocol`을 호출합니다.

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chatHub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> 서버와 마찬가지로 이 `AddMessagePackProtocol` 호출은 옵션을 구성하기 위한 대리자를 전달받습니다.

### <a name="javascript-client"></a>JavaScript 클라이언트

JavaScript 클라이언트에 대 한 지원은 MessagePack가 제공 된 `@aspnet/signalr-protocol-msgpack` npm 패키지 합니다.

```console
npm install @aspnet/signalr-protocol-msgpack
```

npm 패키지를 설치한 뒤에 모듈을 JavaScript 모듈 로더를 통해서 직접 사용할 수도 있고 *node_modules\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js* 파일을 참조해서 브라우저로 가져올 수도 있습니다. 브라우저에서 `msgpack5` 라이브러리도 참조해야 합니다. 사용 된 `<script>` 대 한 참조를 만듭니다. 이 라이브러리는 *node_modules\msgpack5\dist\msgpack5.js*에서 찾을 수 있습니다.

> [!NOTE]
> `<script>` 요소를 사용할 때 순서가 중요합니다. *msgpack5.js*보다 *signalr-protocol-msgpack.js*를 먼저 참조하면 MessagePack을 이용해서 연결하려고 시도할 때 오류가 발생합니다. *signalr.js*도 *signalr-protocol-msgpack.js*보다 먼저 참조해야 합니다.

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

`HubConnectionBuilder`에 `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())`을 추가하면 클라이언트가 서버에 연결할 때 MessagePack 프로토콜을 사용하도록 구성됩니다.

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> 현재 JavaScript 클라이언트에는 MessagePack 프로토콜에 대한 구성 옵션이 존재하지 않습니다.

## <a name="messagepack-quirks"></a>MessagePack 쿼크

MessagePack Hub 프로토콜을 사용 하는 경우 알아야 할 몇 가지 문제가 있습니다.

### <a name="messagepack-is-case-sensitive"></a>MessagePack는 대/소문자 구분

MessagePack 프로토콜은 대/소문자 구분입니다. 예를 들어 다음 C# 클래스:

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

JavaScript 클라이언트에서 전송 하는 경우에 사용 해야 `PascalCased` 속성 이름은 대/소문자와 일치 해야 합니다는 C# 클래스를 정확 하 게 합니다. 예를 들어:

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

사용 하 여 `camelCased` 이름을 않습니다 바인딩할 합니다 C# 클래스입니다. 사용 하 여이 문제를 해결 해도 `Key` MessagePack 속성에 대해 다른 이름을 지정 하는 특성입니다. 자세한 내용은 [MessagePack CSharp 설명서](https://github.com/neuecc/MessagePack-CSharp#object-serialization)합니다.

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a>직렬화/역직렬화 DateTime.Kind 유지 되지 않습니다.

MessagePack 프로토콜을 인코딩하는 방법을 제공 하지 않습니다 합니다 `Kind` 의 값을 `DateTime`입니다. 결과적으로 날짜를 역직렬화 하는 동안, MessagePack 허브 프로토콜 들어오는 날짜가 UTC 형식으로 가정 합니다. 사용 하는 경우 `DateTime` 현지 시간 값을 권장 보내기 전에 UTC로 변환 합니다. 변환할 UTC에서 현지 시간을 받을 때마다 합니다.

이 제한에 대 한 자세한 내용은 GitHub을 참조 하세요. 문제가 [aspnet/SignalR #2632](https://github.com/aspnet/SignalR/issues/2632)합니다.

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a>DateTime.MinValue는 MessagePack javascript에서에서 지원 되지 않습니다.

합니다 [msgpack5](https://github.com/mcollina/msgpack5) SignalR JavaScript 클라이언트에서 사용 되는 라이브러리를 지원 하지 않습니다는 `timestamp96` MessagePack의 형식입니다. 이 형식은 매우 큰 날짜 값 (또는 과거의 초기에 나중에 매우 많이)를 인코딩하는 데 사용 됩니다. 변수의 `DateTime.MinValue` 됩니다 `January 1, 0001` 는 인코딩해야는 `timestamp96` 값입니다. 이 전송으로 인해 `DateTime.MinValue` 클라이언트 JavaScript에 지원 되지 않습니다. 때 `DateTime.MinValue` 수신, JavaScript 클라이언트에서 다음 오류가 throw 됩니다.

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

일반적으로 `DateTime.MinValue` 인코딩하는 데 사용 되는 "missing" 또는 `null` 값입니다. MessagePack에서 해당 값을 인코드 해야 할 경우 사용 하 여 null을 허용 `DateTime` 값 (`DateTime?`) 또는 별도 인코딩 `bool` 날짜 있는지를 나타내는 값입니다.

이 제한에 대 한 자세한 내용은 GitHub을 참조 하세요. 문제가 [aspnet/SignalR #2228](https://github.com/aspnet/SignalR/issues/2228)합니다.

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a>"미리-" 컴파일 환경에서 MessagePack 지원

합니다 [MessagePack CSharp](https://github.com/neuecc/MessagePack-CSharp) 라이브러리.NET 클라이언트 및 서버에서 사용 하는 serialization을 최적화 하기 위해 코드 생성을 사용 합니다. 결과적으로, "미리-" 컴파일 (예: Xamarin iOS 또는 Unity)를 사용 하는 환경에서 기본적으로 지원 되지 않습니다. "미리 코드를 생성할" serializer/deserializer에서 MessagePack 이러한 환경에서 사용 하는 것이 가능 합니다. 자세한 내용은 [MessagePack CSharp 설명서](https://github.com/neuecc/MessagePack-CSharp#pre-code-generationunityxamarin-supports)합니다. 직렬 변환기를 미리 생성 한 후에 전달 된 구성 대리자를 사용 하 여 등록할 수 있습니다 `AddMessagePackProtocol`:

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

### <a name="type-checks-are-more-strict-in-messagepack"></a>형식 검사는 더 엄격 하 게 MessagePack

JSON Hub 프로토콜을 역직렬화 하는 동안 형식 변환을 수행 합니다. 예를 들어 들어오는 개체 속성 값이 있는 숫자 인지 (`{ foo: 42 }`) 형식의.NET 클래스의 속성 이지만 `string`, 값 변환 됩니다. 그러나 MessagePack이이 변환을 수행 하지 않습니다 하 고 서버 쪽 로그 (및 콘솔)에서 볼 수 있는 예외를 throw 합니다.

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

이 제한에 대 한 자세한 내용은 GitHub을 참조 하세요. 문제가 [aspnet/SignalR #2937](https://github.com/aspnet/SignalR/issues/2937)합니다.

## <a name="related-resources"></a>관련 참고 자료

* [시작](xref:tutorials/signalr)
* [.NET 클라이언트](xref:signalr/dotnet-client)
* [JavaScript 클라이언트](xref:signalr/javascript-client)
