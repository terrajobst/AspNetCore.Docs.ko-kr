---
title: .NET 용 gRPC 구성
author: jamesnk
description: .NET 앱 용 gRPC를 구성 하는 방법에 대해 알아봅니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 09/05/2019
uid: grpc/configuration
ms.openlocfilehash: de478752f94713d7f3d55ed66e6410500c3a72e8
ms.sourcegitcommit: 2cb857f0de774df421e35289662ba92cfe56ffd1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75355727"
---
# <a name="grpc-for-net-configuration"></a>.NET 용 gRPC 구성

## <a name="configure-services-options"></a>서비스 옵션 구성

gRPC 서비스는 *Startup.cs*에서 `AddGrpc`를 사용 하 여 구성 됩니다. 다음 표에서는 gRPC 서비스를 구성 하는 옵션을 설명 합니다.

| 옵션 | 기본값 | 설명 |
| ------ | ------------- | ----------- |
| `MaxSendMessageSize` | `null` | 서버에서 보낼 수 있는 최대 메시지 크기 (바이트)입니다. 구성 된 최대 메시지 크기를 초과 하는 메시지를 전송 하려고 하면 예외가 발생 합니다. |
| `MaxReceiveMessageSize` | 4MB | 서버에서 받을 수 있는 최대 메시지 크기 (바이트)입니다. 서버에서이 한도를 초과 하는 메시지를 수신 하는 경우 예외가 throw 됩니다. 이 값을 늘리면 서버가 더 큰 메시지를 수신할 수 있지만 메모리 소비에 부정적인 영향을 줄 수 있습니다. |
| `EnableDetailedErrors` | `false` | `true`경우 서비스 메서드에서 예외가 throw 되 면 자세한 예외 메시지가 클라이언트에 반환 됩니다. 기본값은 `false`입니다. `EnableDetailedErrors`를 `true`로 설정 하면 중요 한 정보가 누출 될 수 있습니다. |
| `CompressionProviders` | gzip | 메시지를 압축 하 고 압축을 푸는 데 사용 되는 압축 공급자의 컬렉션입니다. 사용자 지정 압축 공급자를 만들어 컬렉션에 추가할 수 있습니다. 구성 된 기본 공급자는 **gzip** 압축을 지원 합니다. |
| `ResponseCompressionAlgorithm` | `null` | 서버에서 보낸 메시지를 압축 하는 데 사용 되는 압축 알고리즘입니다. 알고리즘은 `CompressionProviders`의 압축 공급자와 일치 해야 합니다. 응답을 압축 하는 알고리즘의 경우 클라이언트는 **grpc-수락-인코딩** 헤더에 알고리즘을 전송 하 여 알고리즘을 지원 하도록 지정 해야 합니다. |
| `ResponseCompressionLevel` | `null` | 서버에서 보낸 메시지를 압축 하는 데 사용 되는 압축 수준입니다. |
| `Interceptors` | None | 각 gRPC 호출을 사용 하 여 실행 되는 인터셉터의 컬렉션입니다. 인터셉터는 등록 된 순서 대로 실행 됩니다. 전역적으로 구성 된 인터셉터는 단일 서비스에 대해 구성 된 인터셉터 보다 먼저 실행 됩니다. GRPC 인터셉터에 대 한 자세한 내용은 [Grpc 인터셉터 및 미들웨어](xref:grpc/migration#grpc-interceptors-vs-middleware)를 참조 하세요. |

`Startup.ConfigureServices`에서 `AddGrpc` 호출에 옵션 대리자를 제공 하 여 모든 서비스에 대해 옵션을 구성할 수 있습니다.

[!code-csharp[](~/grpc/configuration/sample/GrcpService/Startup.cs?name=snippet)]

단일 서비스에 대 한 옵션은 `AddGrpc`에서 제공 하는 전역 옵션을 재정의 하며 `AddServiceOptions<TService>`를 사용 하 여 구성할 수 있습니다.

[!code-csharp[](~/grpc/configuration/sample/GrcpService/Startup2.cs?name=snippet)]

## <a name="configure-client-options"></a>클라이언트 옵션 구성

gRPC 클라이언트 구성이 `GrpcChannelOptions`에 설정 되어 있습니다. 다음 표에서는 gRPC 채널을 구성 하는 옵션을 설명 합니다.

| 옵션 | 기본값 | 설명 |
| ------ | ------------- | ----------- |
| `HttpClient` | 새 인스턴스 | GRPC 호출을 수행 하는 데 사용 되는 `HttpClient`입니다. 클라이언트를 설정 하 여 사용자 지정 `HttpClientHandler`를 구성 하거나 gRPC 호출에 대 한 HTTP 파이프라인에 추가 처리기를 추가할 수 있습니다. `HttpClient` 지정 하지 않으면 채널에 대 한 새 `HttpClient` 인스턴스가 만들어집니다. 자동으로 삭제 됩니다. |
| `DisposeHttpClient` | `false` | `true``HttpClient` 지정 된 경우 `GrpcChannel` 삭제 되 면 `HttpClient` 인스턴스가 삭제 됩니다. |
| `LoggerFactory` | `null` | 클라이언트에서 gRPC 호출에 대 한 정보를 기록 하는 데 사용 하는 `LoggerFactory`입니다. `LoggerFactory` 인스턴스는 종속성 주입에서 확인 하거나 `LoggerFactory.Create`를 사용 하 여 만들 수 있습니다. 로깅 구성 예제는 <xref:grpc/diagnostics#grpc-client-logging>를 참조 하세요. |
| `MaxSendMessageSize` | `null` | 클라이언트에서 보낼 수 있는 최대 메시지 크기 (바이트)입니다. 구성 된 최대 메시지 크기를 초과 하는 메시지를 전송 하려고 하면 예외가 발생 합니다. |
| `MaxReceiveMessageSize` | 4MB | 클라이언트에서 받을 수 있는 최대 메시지 크기 (바이트)입니다. 클라이언트에서이 한도를 초과 하는 메시지를 수신 하면 예외가 throw 됩니다. 이 값을 늘리면 클라이언트는 더 큰 메시지를 받을 수 있지만 메모리 사용에 부정적인 영향을 줄 수 있습니다. |
| `Credentials` | `null` | `ChannelCredentials` 인스턴스입니다. 자격 증명은 gRPC 호출에 인증 메타 데이터를 추가 하는 데 사용 됩니다. |
| `CompressionProviders` | gzip | 메시지를 압축 하 고 압축을 푸는 데 사용 되는 압축 공급자의 컬렉션입니다. 사용자 지정 압축 공급자를 만들어 컬렉션에 추가할 수 있습니다. 구성 된 기본 공급자는 **gzip** 압축을 지원 합니다. |

다음 예를 참조하십시오.

* 채널의 최대 송신 및 수신 메시지 크기를 설정 합니다.
* 클라이언트를 만듭니다.

[!code-csharp[](~/grpc/configuration/sample/Program.cs?name=snippet&highlight=3-8)]

[!INCLUDE[](~/includes/gRPCazure.md)]

## <a name="additional-resources"></a>추가 자료

* <xref:grpc/aspnetcore>
* <xref:grpc/client>
* <xref:grpc/diagnostics>
* <xref:tutorials/grpc/grpc-start>
