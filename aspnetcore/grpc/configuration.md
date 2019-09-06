---
title: ASP.NET Core 구성을 위한 gRPC
author: jamesnk
description: ASP.NET Core 앱에 대 한 gRPC를 구성 하는 방법에 대해 알아봅니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 08/21/2019
uid: grpc/configuration
ms.openlocfilehash: 34eb598211c87fbb2c68ae5e041da50d02f543f7
ms.sourcegitcommit: 8b36f75b8931ae3f656e2a8e63572080adc78513
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/05/2019
ms.locfileid: "70310309"
---
# <a name="grpc-for-aspnet-core-configuration"></a>ASP.NET Core 구성을 위한 gRPC

## <a name="configure-services-options"></a>서비스 옵션 구성

다음 표에서는 gRPC 서비스를 구성 하는 옵션을 설명 합니다.

| 옵션 | 기본값 | 설명 |
| ------ | ------------- | ----------- |
| `MaxSendMessageSize` | `null` | 서버에서 보낼 수 있는 최대 메시지 크기 (바이트)입니다. 구성 된 최대 메시지 크기를 초과 하는 메시지를 전송 하려고 하면 예외가 발생 합니다. |
| `MaxReceiveMessageSize` | 4MB | 서버에서 받을 수 있는 최대 메시지 크기 (바이트)입니다. 서버에서이 한도를 초과 하는 메시지를 수신 하는 경우 예외가 throw 됩니다. 이 값을 늘리면 서버가 더 큰 메시지를 수신할 수 있지만 메모리 소비에 부정적인 영향을 줄 수 있습니다. |
| `EnableDetailedErrors` | `false` | 인 `true`경우 서비스 메서드에서 예외가 throw 되 면 자세한 예외 메시지가 클라이언트에 반환 됩니다. 기본값은 `false`입니다. `EnableDetailedErrors` 로`true` 설정 하면 중요 한 정보가 누출 될 수 있습니다. |
| `CompressionProviders` | gzip, deflate | 메시지를 압축 하 고 압축을 푸는 데 사용 되는 압축 공급자의 컬렉션입니다. 사용자 지정 압축 공급자를 만들어 컬렉션에 추가할 수 있습니다. 구성 된 기본 공급자는 **gzip** 및 **deflate** 압축을 지원 합니다. |
| `ResponseCompressionAlgorithm` | `null` | 서버에서 보낸 메시지를 압축 하는 데 사용 되는 압축 알고리즘입니다. 알고리즘은의 `CompressionProviders`압축 공급자와 일치 해야 합니다. 응답을 압축 하는 알고리즘의 경우 클라이언트는 **grpc-수락-인코딩** 헤더에 알고리즘을 전송 하 여 알고리즘을 지원 하도록 지정 해야 합니다. |
| `ResponseCompressionLevel` | `null` | 서버에서 보낸 메시지를 압축 하는 데 사용 되는 압축 수준입니다. |

`AddGrpc` 에서`Startup.ConfigureServices`호출에 대 한 옵션 대리자를 제공 하 여 모든 서비스에 대해 옵션을 구성할 수 있습니다.

[!code-csharp[](~/grpc/configuration/sample/GrcpService/Startup.cs?name=snippet)]

단일 서비스에 대 한 옵션은에서 `AddGrpc` 제공 하는 전역 옵션을 재정의 하 고 다음을 사용 하 여 `AddServiceOptions<TService>`구성할 수 있습니다.

[!code-csharp[](~/grpc/configuration/sample/GrcpService/Startup2.cs?name=snippet)]

## <a name="configure-client-options"></a>클라이언트 옵션 구성

다음 표에서는 gRPC 채널을 구성 하는 옵션을 설명 합니다.

| 옵션 | 기본값 | 설명 |
| ------ | ------------- | ----------- |
| `HttpClient` | 새 인스턴스 | Grpc 호출을 수행 하는 데 사용 되는 `HttpClient` 입니다. 클라이언트를 설정 하 여 사용자 지정 `HttpClientHandler`을 구성 하거나 grpc 호출에 대 한 HTTP 파이프라인에 추가 처리기를 추가할 수 있습니다. 기본적으로 새 `HttpClient` 인스턴스가 만들어집니다. |
| `MaxSendMessageSize` | `null` | 클라이언트에서 보낼 수 있는 최대 메시지 크기 (바이트)입니다. 구성 된 최대 메시지 크기를 초과 하는 메시지를 전송 하려고 하면 예외가 발생 합니다. |
| `MaxReceiveMessageSize` | 4MB | 클라이언트에서 받을 수 있는 최대 메시지 크기 (바이트)입니다. 서버에서이 한도를 초과 하는 메시지를 수신 하는 경우 예외가 throw 됩니다. 이 값을 늘리면 서버가 더 큰 메시지를 수신할 수 있지만 메모리 소비에 부정적인 영향을 줄 수 있습니다. |
| `TransportOptions` | `null` | 전송 옵션 채널에서 gRPC 서비스를 호출 하는 방법을 구성 합니다. 현재 유일 하 게 구현 `HttpClientTransport` 되는 옵션을 사용 `HttpClient` 하 여 grpc에서 사용 되는를 지정할 수 있습니다. |
| `Credentials` | `null` | `ChannelCredentials` 인스턴스입니다. 자격 증명은 gRPC 호출에 인증 메타 데이터를 추가 하는 데 사용 됩니다. |
| `CompressionProviders` | gzip, deflate | 메시지를 압축 하 고 압축을 푸는 데 사용 되는 압축 공급자의 컬렉션입니다. 사용자 지정 압축 공급자를 만들어 컬렉션에 추가할 수 있습니다. 구성 된 기본 공급자는 **gzip** 및 **deflate** 압축을 지원 합니다. |

다음 예를 참조하십시오.

* 채널의 최대 송신 및 수신 메시지 크기를 설정 합니다.
* 클라이언트를 만듭니다.

[!code-csharp[](~/grpc/configuration/sample/Program.cs?name=snippet&highlight=3-8)]

## <a name="additional-resources"></a>추가 자료

* <xref:grpc/aspnetcore>
* <xref:grpc/client>
* <xref:tutorials/grpc/grpc-start>
