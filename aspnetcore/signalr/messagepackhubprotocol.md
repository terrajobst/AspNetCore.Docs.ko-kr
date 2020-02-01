---
title: ASP.NET Core SignalR에서 MessagePack Hub 프로토콜 사용
author: bradygaster
description: ASP.NET Core SignalR에 MessagePack Hub 프로토콜을 추가 합니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- SignalR
uid: signalr/messagepackhubprotocol
ms.openlocfilehash: 3c2a4285945d3fdc6bba195e3160da8b9dcbba44
ms.sourcegitcommit: 0b0e485a8a6dfcc65a7a58b365622b3839f4d624
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/01/2020
ms.locfileid: "76928183"
---
# <a name="use-messagepack-hub-protocol-in-opno-locsignalr-for-aspnet-core"></a>ASP.NET Core SignalR에서 MessagePack Hub 프로토콜 사용

작성자: [Brennan Conroy](https://github.com/BrennanConroy)

이 문서는 독자가 [시작하기](xref:tutorials/signalr)에서 설명하는 내용을 잘 알고 있다고 가정합니다.

## <a name="what-is-messagepack"></a>MessagePack이란?

[MessagePack](https://msgpack.org/index.html)은 빠르고 간결한 이진 직렬화 포맷입니다. [JSON](https://www.json.org/)에 비해 크기가 작은 메시지를 생성하기 때문에 성능 및 대역폭이 중요한 경우에 유용합니다. 이진 형식이므로 바이트가 MessagePack 파서를 거치지 않는 한 네트워크 추적 및 로그를 살펴보더라도 메시지를 읽을 수 없습니다. SignalR는 MessagePack 형식을 기본적으로 지원 하며 클라이언트 및 서버에서 사용할 수 있는 Api를 제공 합니다.

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

> [!WARNING]
> [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) 을 검토 하 고 권장 되는 패치를 적용 하는 것이 좋습니다. 예를 들어 `MessagePackSecurity.Active` 정적 속성을 `MessagePackSecurity.UntrustedData`로 설정 합니다. `MessagePackSecurity.Active` 설정 하려면 [MessagePack의 1.9 버전](https://www.nuget.org/packages/MessagePack/1.9.3)을 수동으로 설치 해야 합니다. `MessagePack` 1.9. x를 설치 하면 SignalR 버전이 업그레이드 됩니다. `MessagePackSecurity.Active`을 `MessagePackSecurity.UntrustedData`로 설정 하지 않으면 악의적인 클라이언트에서 서비스 거부가 발생할 수 있습니다. 다음 코드와 같이 `Program.Main``MessagePackSecurity.Active`을 설정 합니다.

```csharp
public static void Main(string[] args)
{
  MessagePackSecurity.Active = MessagePackSecurity.UntrustedData;

  CreateHostBuilder(args).Build().Run();
}
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

::: moniker range=">= aspnetcore-3.0"

JavaScript 클라이언트에 대 한 MessagePack 지원은 `@microsoft/signalr-protocol-msgpack` npm 패키지에 의해 제공 됩니다. 명령 셸에서 다음 명령을 실행 하 여 패키지를 설치 합니다.

```console
npm install @microsoft/signalr-protocol-msgpack
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

JavaScript 클라이언트에 대 한 MessagePack 지원은 `@aspnet/signalr-protocol-msgpack` npm 패키지에 의해 제공 됩니다. 명령 셸에서 다음 명령을 실행 하 여 패키지를 설치 합니다.

```console
npm install @aspnet/signalr-protocol-msgpack
```

::: moniker-end

Npm 패키지를 설치한 후 다음 파일을 참조 하 여 JavaScript 모듈 로더를 통해 직접 모듈을 사용 하거나 브라우저로 가져올 수 있습니다.

::: moniker range=">= aspnetcore-3.0"

*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js* 

::: moniker-end

::: moniker range="< aspnetcore-3.0"

*node_modules\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js* 

::: moniker-end

브라우저에서 `msgpack5` 라이브러리도 참조해야 합니다. `<script>` 태그를 사용 하 여 참조를 만듭니다. 이 라이브러리는 *node_modules\msgpack5\dist\msgpack5.js*에서 찾을 수 있습니다.

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

## <a name="messagepack-quirks"></a>MessagePack 특수

MessagePack Hub 프로토콜을 사용할 때 알아야 할 몇 가지 문제가 있습니다.

### <a name="messagepack-is-case-sensitive"></a>MessagePack은 대/소문자를 구분 합니다.

MessagePack 프로토콜은 대/소문자를 구분 합니다. 예를 들어 다음 C# 클래스를 살펴보세요.

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

JavaScript 클라이언트에서 전송 하는 경우 대/소문자가 C# 클래스와 정확 하 게 일치 해야 하므로 `PascalCased` 속성 이름을 사용 해야 합니다. 예를 들면 다음과 같습니다.:

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

`camelCased` 이름을 사용 하면 C# 클래스에 올바르게 바인딩되지 않습니다. `Key` 특성을 사용 하 여 MessagePack 속성에 다른 이름을 지정 하면이 문제를 해결할 수 있습니다. 자세한 내용은 [MessagePack-CSharp 설명서](https://github.com/neuecc/MessagePack-CSharp#object-serialization)를 참조 하세요.

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a>Serialize/deserialize 할 때에는 DateTime이 유지 되지 않습니다.

MessagePack 프로토콜은 `DateTime`의 `Kind` 값을 인코딩하는 방법을 제공 하지 않습니다. 결과적으로, 날짜를 deserialize 할 때 MessagePack Hub 프로토콜은 들어오는 날짜가 UTC 형식인 것으로 가정 합니다. 현지 시간에 `DateTime` 값을 사용 하는 경우에는 값을 보내기 전에 UTC로 변환 하는 것이 좋습니다. 받은 UTC에서 현지 시간으로 변환 합니다.

이 제한 사항에 대 한 자세한 내용은 GitHub 문제 [aspnet/SignalR#2632](https://github.com/aspnet/SignalR/issues/2632)를 참조 하세요.

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a>MinValue는 JavaScript의 MessagePack에서 지원 되지 않습니다.

SignalR JavaScript 클라이언트에서 사용 하는 [msgpack5](https://github.com/mcollina/msgpack5) 라이브러리는 MessagePack에서 `timestamp96` 형식을 지원 하지 않습니다. 이 형식은 매우 큰 날짜 값을 인코딩하는 데 사용 됩니다 (이전에는 매우 일찍 또는 나중에 매우 오래 됨). `DateTime.MinValue` 값은 `timestamp96` 값으로 인코딩해야 하는 `January 1, 0001`입니다. 이로 인해 JavaScript 클라이언트에 `DateTime.MinValue`를 보내는 것은 지원 되지 않습니다. JavaScript 클라이언트에서 `DateTime.MinValue` 받으면 다음 오류가 throw 됩니다.

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

일반적으로 `DateTime.MinValue`는 "missing" 또는 `null` 값을 인코딩하는 데 사용 됩니다. MessagePack에서 해당 값을 인코딩해야 하는 경우 nullable `DateTime` 값 (`DateTime?`)을 사용 하거나 날짜가 있는지를 나타내는 별도의 `bool` 값을 인코딩합니다.

이 제한 사항에 대 한 자세한 내용은 GitHub 문제 [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228)를 참조 하세요.

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a>"사전" 컴파일 환경에서 MessagePack 지원

.NET 클라이언트 및 서버에서 사용 하는 [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8) 라이브러리는 코드 생성을 사용 하 여 serialization을 최적화 합니다. 따라서 "미리" 컴파일 (예: Xamarin iOS 또는 Unity)을 사용 하는 환경에서는 기본적으로 지원 되지 않습니다. 직렬 변환기/역직렬 변환기 코드를 "미리 생성" 하 여 이러한 환경에서 MessagePack를 사용할 수 있습니다. 자세한 내용은 [MessagePack-CSharp 설명서](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8#pre-code-generationunityxamarin-supports)를 참조 하세요. Serializer를 미리 생성 한 후에는 `AddMessagePackProtocol`에 전달 된 구성 대리자를 사용 하 여 해당 serializer를 등록할 수 있습니다.

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

### <a name="type-checks-are-more-strict-in-messagepack"></a>MessagePack에서 형식 검사가 더 엄격 합니다.

JSON 허브 프로토콜은 deserialization 중에 형식 변환을 수행 합니다. 예를 들어 들어오는 개체에 숫자 (`{ foo: 42 }`) 인 속성 값이 있지만 .NET 클래스의 속성이 `string`형식이 면 값이 변환 됩니다. 그러나 MessagePack는이 변환을 수행 하지 않으며 서버 쪽 로그 (및 콘솔)에서 볼 수 있는 예외를 throw 합니다.

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

이 제한 사항에 대 한 자세한 내용은 GitHub 문제 [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937)를 참조 하세요.

## <a name="related-resources"></a>관련 참고 자료

* [시작](xref:tutorials/signalr)
* [.NET 클라이언트](xref:signalr/dotnet-client)
* [JavaScript 클라이언트](xref:signalr/javascript-client)
