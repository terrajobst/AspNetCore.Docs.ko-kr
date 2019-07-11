---
title: ASP.NET Core의 요청 및 응답 작업
author: jkotalik
description: ASP.NET Core에서 요청 본문을 읽고 응답 본문을 쓰는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: jukotali
ms.custom: mvc
ms.date: 02/26/2019
uid: fundamentals/middleware/request-response
ms.openlocfilehash: c9f6509738ef6290666a58268fbb0584913db9d6
ms.sourcegitcommit: 357a7120632b20465801c093e4e5bd4a315496a8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/08/2019
ms.locfileid: "67649237"
---
# <a name="request-and-response-operations-in-aspnet-core"></a>ASP.NET Core의 요청 및 응답 작업

작성자 [Justin Kotalik](https://github.com/jkotalik)

이 문서에서는 요청 본문을 읽고 응답 본문을 쓰는 방법을 설명합니다. 미들웨어를 작성하는 경우 이러한 작업을 위한 코드를 작성해야 할 수도 있습니다. 그렇지 않으면 일반적으로 MVC 및 Razor Pages에서 작업을 처리하므로 이 코드를 작성할 필요가 없습니다.

ASP.NET Core 3.0에는 요청 및 응답 본문에 대한 두 가지 추상(<xref:System.IO.Stream> 및 <xref:System.IO.Pipelines.Pipe>)이 있습니다. 요청 읽기에서 [HttpRequest.Body](xref:Microsoft.AspNetCore.Http.HttpRequest.Body)는 <xref:System.IO.Stream>이고, `HttpRequest.BodyPipe`는 <xref:System.IO.Pipelines.PipeReader>입니다. 응답 쓰기에서 [HttpResponse.Body](xref:Microsoft.AspNetCore.Http.HttpResponse.Body)는 이고, `HttpResponse.BodyPipe`는 <xref:System.IO.Pipelines.PipeWriter>입니다.

스트림보다 파이프라인을 사용하는 것이 좋습니다. 일부 간단한 작업에서는 스트림이 더 편리할 수도 있지만, 파이프라인은 성능상의 장점이 있고 대부분의 시나리오에서 더 편리합니다. 3\.0에서 ASP.NET Core는 내부적으로 스트림 대신 파이프라인을 사용하기 시작했습니다. 다음과 같은 경우를 예로 들 수 있습니다.

- `FormReader`
- `TextReader`
- `TextWriter`
- `HttpResponse.WriteAsync`

스트림이 사라지는 것은 아닙니다. 스트림은 .NET 전체에서 계속 사용되며, `FileStreams` 및 `ResponseCompression`과 같이 상응하는 파이프 항목이 없는 스트림 형식도 많습니다.

## <a name="stream-examples"></a>스트림 예제

새 줄에서 분할하여 전체 요청 본문을 문자열 목록으로 읽는 미들웨어를 만들려 한다고 가정해 보겠습니다. 단순 스트림 구현은 다음 예제와 같이 표시될 수 있습니다.

[!code-csharp[](request-response/samples/3.x/RequestResponseSample/Startup.cs?name=GetListOfStringsFromStream)]

이 코드는 실행되지만 다음과 같은 몇 가지 문제가 있습니다.

- 예제에서는 `StringBuilder`에 추가하기 전에 즉시 제거되는 또 다른 문자열(`encodedString`)이 생성됩니다. 이 프로세스는 스트림의 모든 바이트에 대해 발생하므로 결과는 전체 요청 본문의 크기만큼 추가 메모리가 할당됩니다.
- 예제에서는 새 줄에서 분할하기 전에 전체 문자열을 읽습니다. 바이트 배열에서 새 줄을 확인하는 것이 더 효율적입니다.

이러한 문제 중 일부를 수정한 예제는 다음과 같습니다.

[!code-csharp[](request-response/samples/3.x/RequestResponseSample/Startup.cs?name=GetListOfStringsFromStreamMoreEfficient)]

이 예제는 다음과 같이 작성되었습니다.

- 줄 바꿈 문자가 없는 경우 `StringBuilder`에 전체 요청 본문을 버퍼링하지 않습니다.
- 문자열에서 `Split`를 호출하지 않습니다.

그러나 여전히 다음과 같은 몇 가지 문제가 있습니다.

- 줄 바꿈 문자가 스파스인 경우, 대부분의 요청 본문이 문자열에 버퍼링됩니다.
- 또한 여전히 문자열(`remainingString`)을 만들어 문자열 버퍼에 추가하므로 추가 할당이 발생합니다.

이러한 문제는 수정할 수 있지만, 코드가 개선되는 사항도 없이 점점 더 복잡해집니다. 파이프라인은 코드 복잡성을 최소화하여 이러한 문제를 해결하는 방법을 제공합니다.

## <a name="pipelines"></a>파이프라인

다음 예제에서는 `PipeReader`를 사용하여 동일한 시나리오를 처리하는 방법을 보여 줍니다.

[!code-csharp[](request-response/samples/3.x/RequestResponseSample/Startup.cs?name=GetListOfStringFromPipe)]

이 예제에서는 스트림 구현에 포함된 많은 문제를 수정합니다.

- `PipeReader`에서 사용되지 않은 바이트를 처리하므로 문자열 버퍼가 필요하지 않습니다.
- 인코드된 문자열은 반환된 문자열 목록에 직접 추가됩니다.
- 문자열에서 사용하는 메모리 외에 할당 없이 문자열이 생성됩니다(`ToArray()` 호출 제외).

## <a name="adapters"></a>어댑터

이제 `HttpRequest` 및 `HttpResponse`에서 `Body` 및 `BodyPipe` 속성을 둘 다 사용할 수 있으므로 `Body`를 다른 스트림으로 설정하면 어떻게 될까요? 3\.0에서는 새 어댑터 세트가 각 유형을 다른 유형에 맞게 자동으로 조정합니다. 예를 들어 `HttpRequest.Body`을 새 스트림으로 설정하는 경우 `HttpRequest.BodyPipe`가 `HttpRequest.Body`를 래핑하는 새 `PipeReader`으로 자동 설정됩니다. `BodyPipe` 속성을 설정하는 경우에도 동일한 동작이 적용됩니다. `HttpResponse.BodyPipe`를 새 `PipeWriter`로 설정하면 `HttpResponse.Body`가 `HttpResponse.BodyPipe`를 래핑하는 새 스트림으로 자동 설정됩니다.

## <a name="startasync"></a>StartAsync

`HttpResponse.StartAsync`는 3.0의 새로운 기능입니다. 헤더를 수정할 수 없음을 나타내고 `OnStarting` 콜백을 실행하는 데 사용됩니다. 3\.0-preview3에서는 `HttpRequest.BodyPipe`를 사용하기 전에 `StartAsync`을 호출해야 하며, 이후 릴리스에서는 권장 사항이 될 예정입니다. Kestrel을 서버로 사용하는 경우 `PipeReader`를 사용하기 전에 StartAsync를 호출하면 `GetMemory`에서 반환된 메모리가 외부 버퍼가 아닌 Kestrel의 내부 <xref:System.IO.Pipelines.Pipe>에 속하게 됩니다.

## <a name="additional-resources"></a>추가 자료

- [System.IO.Pipelines 소개](https://devblogs.microsoft.com/dotnet/system-io-pipelines-high-performance-io-in-net/)
- <xref:fundamentals/middleware/write>
