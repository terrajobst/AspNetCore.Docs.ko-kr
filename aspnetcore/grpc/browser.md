---
title: 브라우저 앱에서 gRPC 사용
author: jamesnk
description: GRPC-웹을 사용 하 여 브라우저 앱에서 호출할 수 있도록 ASP.NET Core에서 gRPC 서비스를 구성 하는 방법에 대해 알아봅니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 02/10/2020
uid: grpc/browser
ms.openlocfilehash: 333fc8c4277bbac47042d4904c276e963186914a
ms.sourcegitcommit: 85564ee396c74c7651ac47dd45082f3f1803f7a2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77172269"
---
# <a name="use-grpc-in-browser-apps"></a>브라우저 앱에서 gRPC 사용

별 [뉴턴-킹](https://twitter.com/jamesnk)

> [!IMPORTANT]
> **gRPC-.NET의 웹 지원이 실험적입니다.**
>
> gRPC-웹 .NET은 커밋된 제품이 아니라 실험적 프로젝트입니다. 다음 작업을 수행 합니다.
>
> * GRPC 구현 방법이 적합 한지 테스트 합니다.
> * 이 방법이 .NET 개발자에 게 도움이 되는 경우 프록시를 통해 gRPC-웹을 설정 하는 일반적인 방법에 대 한 의견을 받으세요.
>
> 개발자가 사용 하 고 생산성을 높일 수 있도록 [https://github.com/grpc/grpc-dotnet](https://github.com/grpc/grpc-dotnet) 에서 피드백을 남겨 주세요.

브라우저 기반 앱에서 HTTP/2 gRPC 서비스를 호출할 수 없습니다. [grpc-웹](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md) 은 브라우저 JavaScript 및 Blazor Apps에서 grpc 서비스를 호출할 수 있도록 하는 프로토콜입니다. 이 문서에서는 .NET Core에서 gRPC-웹을 사용 하는 방법을 설명 합니다.

## <a name="configure-grpc-web-in-aspnet-core"></a>ASP.NET Core에서 gRPC-웹 구성

ASP.NET Core에서 호스트 되는 gRPC 서비스는 HTTP/2 gRPC와 함께 gRPC-웹을 지원 하도록 구성할 수 있습니다. gRPC-웹은 서비스를 변경할 필요가 없습니다. 유일한 수정 사항은 시작 구성입니다.

ASP.NET Core gRPC 서비스에서 gRPC-웹을 사용 하도록 설정 하려면 다음을 수행 합니다.

* [AspNetCore](https://www.nuget.org/packages/Grpc.AspNetCore.Web) 패키지에 대 한 참조를 추가 합니다.
* *Startup.cs*에 `AddGrpcWeb` 및 `UseGrpcWeb`를 추가 하 여 grpc-웹을 사용 하도록 앱을 구성 합니다.

[!code-csharp[](~/grpc/browser/sample/Startup.cs?name=snippet_1&highlight=10,14)]

위의 코드는:

* 라우팅 후 및 끝점 앞에 gRPC-웹 미들웨어, `UseGrpcWeb`을 추가 합니다.
* `EnableGrpcWeb`에서 gRPC-웹을 지 원하는 `endpoints.MapGrpcService<GreeterService>()` 방법을 지정 합니다. 

또는 ConfigureServices에 `services.AddGrpcWeb(o => o.GrpcWebEnabled = true);`을 추가 하 여 gRPC-웹을 지원 하도록 모든 서비스를 구성 합니다.

[!code-csharp[](~/grpc/browser/sample/AllServicesSupportExample_Startup.cs?name=snippet_1&highlight=6,13)]

CORS를 지원 하도록 ASP.NET Core를 구성 하는 등 브라우저에서 gRPC-웹을 호출 하려면 몇 가지 추가 구성이 필요할 수 있습니다. 자세한 내용은 [CORS 지원](xref:security/cors)을 참조 하세요.

## <a name="call-grpc-web-from-the-browser"></a>브라우저에서 gRPC-웹 호출

브라우저 앱은 gRPC-웹을 사용 하 여 gRPC 서비스를 호출할 수 있습니다. 브라우저에서 gRPC를 사용 하 여 gRPC 서비스를 호출할 때 몇 가지 요구 사항 및 제한 사항이 있습니다.

* 서버는 gRPC-웹을 지원 하도록 구성 되어 있어야 합니다.
* 클라이언트 스트리밍 및 양방향 스트리밍 호출은 지원 되지 않습니다. 서버 스트리밍이 지원 됩니다.
* 다른 도메인에서 gRPC 서비스를 호출 하려면 서버에 [CORS](xref:security/cors) 를 구성 해야 합니다.

### <a name="javascript-grpc-web-client"></a>JavaScript gRPC-웹 클라이언트

JavaScript gRPC-웹 클라이언트가 있습니다. JavaScript에서 gRPC-Web을 사용 하는 방법에 대 한 지침은 [gRPC를 사용 하 여 javascript 클라이언트 코드 작성-웹](https://github.com/grpc/grpc-web/tree/master/net/grpc/gateway/examples/helloworld#write-client-code)을 참조 하세요.

### <a name="configure-grpc-web-with-the-net-grpc-client"></a>.NET gRPC 클라이언트를 사용 하 여 gRPC-웹 구성

.NET gRPC 클라이언트는 gRPC-웹 호출을 만들도록 구성할 수 있습니다. 이는 브라우저에서 호스트 되 고 JavaScript 코드와 동일한 HTTP 제한이 있는 [Blazor Weasembomapps](xref:blazor/index#blazor-webassembly) 에 유용 합니다. .NET 클라이언트를 사용 하 여 gRPC-웹을 호출 하는 것은 [HTTP/2 gRPC와 동일 합니다](xref:grpc/client). 채널을 만드는 방법만 수정 하면 됩니다.

GRPC-웹을 사용 하려면:

* [Grpc .net. 클라이언트 웹](https://www.nuget.org/packages/Grpc.Net.Client.Web) 패키지에 대 한 참조를 추가 합니다.
* [Grpc .net](https://www.nuget.org/packages/Grpc.Net.Client) 에 대 한 참조를 확인 합니다. 클라이언트 패키지는 2.27.0 이상입니다.
* `GrpcWebHandler`를 사용 하도록 채널을 구성 합니다.

[!code-csharp[](~/grpc/browser/sample/Handler.cs?name=snippet_1)]

위의 코드는:

* GRPC-웹을 사용 하도록 채널을 구성 합니다.
* 클라이언트를 만들고 채널을 사용 하 여 호출 합니다.

`GrpcWebHandler`은 만들 때 다음 구성 옵션을 포함 합니다.

* **Innerhandler**: GRPC HTTP 요청 (예: `HttpClientHandler`)을 수행 하는 기본 <xref:System.Net.Http.HttpMessageHandler>입니다.
* **Mode**: GRPC HTTP 요청 요청 `Content-Type` `application/grpc-web` 또는 `application/grpc-web-text`인지 여부를 지정 하는 열거형 형식입니다.
    * `GrpcWebMode.GrpcWeb`은 인코딩을 사용 하지 않고 콘텐츠를 보내도록 구성 합니다. 기본값.
    * `GrpcWebMode.GrpcWebText`는 base64 인코딩 되도록 콘텐츠를 구성 합니다. 브라우저에서 서버 스트리밍 호출에 필요 합니다.
* **HttpVersion**: http 프로토콜 `Version` 기본 grpc http 요청에 [HttpRequestMessage](xref:System.Net.Http.HttpRequestMessage.Version) 를 설정 하는 데 사용 됩니다. gRPC-웹은 특정 버전이 필요 하지 않으며 지정 되지 않은 경우 기본값을 재정의 하지 않습니다.

> [!IMPORTANT]
> 생성 된 gRPC 클라이언트는 동기화 및 비동기 메서드를 호출 하 여 단항 메서드를 호출 합니다. 예를 들어 `SayHello` 동기화 되 고 `SayHelloAsync`는 비동기입니다. Blazor Weasembomapp에서 동기화 메서드를 호출 하면 앱이 응답 하지 않게 됩니다. 비동기 메서드는 항상 Blazor Weasembmba에서 사용 해야 합니다.

## <a name="additional-resources"></a>추가 자료

* [웹 클라이언트용 gRPC GitHub 프로젝트](https://github.com/grpc/grpc-web)
* <xref:security/cors>
