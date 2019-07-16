---
title: C#을 사용하는 gRPC 서비스
author: juntaoluo
description: GRPC 서비스를 작성할 때 기본 개념을 알아보고 C#입니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 03/31/2019
uid: grpc/basics
ms.openlocfilehash: 78d744d641396c449a142375c69730333f8183cd
ms.sourcegitcommit: 1bf80f4acd62151ff8cce517f03f6fa891136409
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/15/2019
ms.locfileid: "68223878"
---
# <a name="grpc-services-with-c"></a>C 사용 하 여 gRPC 서비스\#

이 문서를 작성 하는 데 필요한 개념에 간략하게 설명 [gRPC](https://grpc.io/docs/guides/) 앱에서 C#합니다. 여기서 다루는 항목 둘 다에 적용 [C-core](https://grpc.io/blog/grpc-stacks)-gRPC 기반 및 ASP.NET Core 기반 앱.

## <a name="proto-file"></a>proto 파일

gRPC는 API 개발에는 계약 중심 접근 방식을 사용합니다. 프로토콜 버퍼 (protobuf)는 기본적으로 디자인 언어 IDL (Interface)를 사용 됩니다. 합니다 *.proto* 파일 포함:

* GRPC 서비스의 정의입니다.
* 클라이언트와 서버 간에 전송 된 메시지입니다.

Protobuf 파일의 구문에 자세한 내용은 참조는 [공식 설명서 (protobuf)](https://developers.google.com/protocol-buffers/docs/proto3)합니다.

예를 들어 합니다 *greet.proto* 에서 사용 되는 파일 [gRPC service 시작](xref:tutorials/grpc/grpc-start):

* 정의 `Greeter` 서비스입니다.
* 합니다 `Greeter` 서비스 정의 `SayHello` 호출 합니다.
* `SayHello` 전송 된 `HelloRequest` 받고 메시지는 `HelloReply` 메시지:

[!code-protobuf[](~/tutorials//grpc/grpc-start/sample/GrpcGreeter/Protos/greet.proto)]

## <a name="add-a-proto-file-to-a-c-app"></a>C.proto 파일로 추가할\# 앱

합니다 *.proto* 파일을 프로젝트에 추가 하 여 포함 된 `<Protobuf>` 항목 그룹:

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=2&range=7-9)]

## <a name="c-tooling-support-for-proto-files"></a>C#도구.proto 파일에 대 한 지원

도구 패키지 [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/) 생성 하는 데 필요한 합니다 C# 에서 자산 *.proto* 파일입니다. 생성 된 자산 (파일):

* 에 생성 됩니다는 필요에 따라 프로젝트를 빌드할 때마다.
* 프로젝트에 추가 하거나 원본 제어에 체크 인 되지 않습니다.
* 에 포함 된 빌드 아티팩트를은 *obj* 디렉터리입니다.

서버 및 클라이언트 프로젝트에서이 패키지가 필요 합니다. `Grpc.Tools` Visual Studio에서 패키지 관리자를 사용 하거나 추가 하 여 추가할 수는 `<PackageReference>` 프로젝트 파일에:

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=1&range=15)]

도구 패키지는 런타임에 필요하지 않으므로 종속성은 `PrivateAssets="All"`로 표시됩니다.

## <a name="generated-c-assets"></a>생성 된 C# 자산

도구 패키지를 생성 합니다 C# 형식 정의에 포함 된 메시지를 나타내는 *.proto* 파일입니다.

서버 쪽 자산에 대 한 추상 서비스 기본 형식에는 생성 됩니다. 기본 형식 gRPC 호출에 포함 된 모든 정의 포함 합니다 *.proto* 파일입니다. 이 기본 형식에서 파생 되며 gRPC 호출에 대 한 논리를 구현 하는 구체적인 서비스 구현을 만듭니다. 에 대 한 합니다 `greet.proto`,이 예제에서는 앞에서 설명한 추상 `GreeterBase` 가상 포함 하는 형식 `SayHello` 메서드가 생성 됩니다. 구체적인 구현을 `GreeterService` 메서드를 재정의 하 고 gRPC 호출을 처리 하는 논리를 구현 합니다.

[!code-csharp[](~/tutorials//grpc/grpc-start/sample/GrpcGreeter/Services/GreeterService.cs?name=snippet)]

클라이언트 쪽 자산에 대 한 구체적인 클라이언트 형식을 생성 됩니다. 는 gRPC를 호출 합니다 *.proto* 파일 메서드를 호출할 수 있는 구체적인 형식으로 변환 됩니다. 에 대 한는 `greet.proto`, 예제에서는 앞에서 설명한 구체적인 `GreeterClient` 형식이 생성 됩니다. 호출 `GreeterClient.SayHello` 서버로 gRPC 호출을 시작 합니다.

[!code-csharp[](~/tutorials//grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?highlight=5-8&name=snippet)]

기본적으로 서버 및 클라이언트 자산 각각에 대해 생성 됩니다 *.proto* 에 포함 된 파일을 `<Protobuf>` 항목 그룹입니다. 서버 자산에만 서버 프로젝트에서 생성 되는 `GrpcServices` 특성이로 설정 된 `Server`합니다.

[!code-xml[](~/tutorials//grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=2&range=7-9)]

특성이로 설정 되는 마찬가지로 `Client` 클라이언트 프로젝트에서.

## <a name="additional-resources"></a>추가 자료

* <xref:grpc/index>
* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/aspnetcore>
* <xref:grpc/migration>
