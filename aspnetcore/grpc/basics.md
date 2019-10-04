---
title: C#을 사용하는 gRPC 서비스
author: juntaoluo
description: 를 사용 하 여 gRPC 서비스를 C#작성할 때 기본 개념을 알아보세요.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 07/03/2019
uid: grpc/basics
ms.openlocfilehash: 8d99d036fd4b00fc4568e67ea5225dc006dea4b1
ms.sourcegitcommit: 73e255e846e414821b8cc20ffa3aec946735cd4e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/03/2019
ms.locfileid: "71925182"
---
# <a name="grpc-services-with-c"></a>C를 사용 하는 gRPC 서비스\#

이 문서에서는에서 C# [grpc](https://grpc.io/docs/guides/) 앱을 작성 하는 데 필요한 개념을 간략하게 설명 합니다. 여기에서 설명 하는 항목은 [C 코어](https://grpc.io/blog/grpc-stacks)기반 및 ASP.NET Core 기반 grpc 앱 모두에 적용 됩니다.

## <a name="proto-file"></a>프로토콜 파일

gRPC는 API 개발에 계약 중심 접근 방식을 사용합니다. 프로토콜 버퍼 (protobuf)는 기본적으로 IDL (인터페이스 디자인 언어)로 사용 됩니다. *@No__t -1* 파일에는 다음이 포함 됩니다.

* GRPC 서비스의 정의입니다.
* 클라이언트와 서버 간에 전송 되는 메시지입니다.

Protobuf 파일의 구문에 대 한 자세한 내용은 [공식 설명서 (protobuf)](https://developers.google.com/protocol-buffers/docs/proto3)를 참조 하세요.

예를 들어 [gRPC 서비스 시작](xref:tutorials/grpc/grpc-start)에 사용 되는 *인사* 파일을 사용 하는 것이 좋습니다.

* 서비스를 `Greeter` 정의 합니다.
* 서비스 `Greeter` 는 호출을 `SayHello` 정의 합니다.
* `SayHello`메시지를 `HelloRequest` 보내고 메시지를 `HelloReply` 받습니다.

[!code-protobuf[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Protos/greet.proto)]

## <a name="add-a-proto-file-to-a-c-app"></a>C\# 앱에 proto 파일 추가

*@No__t -1* 파일은 `<Protobuf>` 항목 그룹에 추가 하 여 프로젝트에 포함 됩니다.

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=2&range=7-9)]

## <a name="c-tooling-support-for-proto-files"></a>.proto 파일의 C# 도구 지원

[도구 패키지 Grpc](https://www.nuget.org/packages/Grpc.Tools/) 는 C# *@no__t -3* 파일에서 자산을 생성 하는 데 필요 합니다. 생성 된 자산 (파일):

* 는 프로젝트가 빌드될 때마다 필요에 따라 생성 됩니다.
* 프로젝트에 추가 되거나 소스 제어에 체크 인 되지 않습니다.
* 는 *obj* 디렉터리에 포함 된 빌드 아티팩트입니다.

이 패키지는 서버 및 클라이언트 프로젝트 모두에 필요 합니다. 메타 패키지에는에 대 `Grpc.Tools`한 참조가 포함 되어 있습니다. `Grpc.AspNetCore` 서버 프로젝트는 Visual `Grpc.AspNetCore` Studio의 패키지 관리자를 사용 하거나 프로젝트 파일에를 `<PackageReference>` 추가 하 여 추가할 수 있습니다.

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=1&range=12)]

클라이언트 프로젝트는 gRPC 클라이언트를 사용 하는 데 필요한 다른 패키지와 함께 `Grpc.Tools`을 직접 참조 해야 합니다. 도구 패키지는 런타임에 필요 하지 않으므로 종속성은 `PrivateAssets="All"`다음과 같이 표시 됩니다.

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/GrpcGreeterClient.csproj?highlight=3&range=9-11)]

## <a name="generated-c-assets"></a>생성 C# 된 자산

도구 패키지는 포함 된 C# *@no__t -2* 파일에 정의 된 메시지를 나타내는 형식을 생성 합니다.

서버 쪽 자산의 경우 추상 서비스 기본 유형이 생성 됩니다. 기본 형식에는 .pa *파일에* 포함 된 모든 grpc 호출의 정의가 포함 됩니다. 이 기본 형식에서 파생 되 고 gRPC 호출에 대 한 논리를 구현 하는 구체적 서비스 구현을 만듭니다. 에 대해 앞에서 설명한 예제에는 가상 `SayHello` 메서드 `GreeterBase` 를 포함 하는 추상 형식이 생성 됩니다. `greet.proto` 구체적 구현은 `GreeterService` 메서드를 재정의 하 고 grpc 호출을 처리 하는 논리를 구현 합니다.

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Services/GreeterService.cs?name=snippet)]

클라이언트 쪽 자산의 경우 구체적인 클라이언트 형식이 생성 됩니다. .Pa *파일의* grpc 호출은 구체적 형식에서 메서드로 변환 되며이를 호출할 수 있습니다. 에 대해 앞에서 설명한 예제는 구체적 `GreeterClient` 형식이 생성 됩니다. `greet.proto` 를 `GreeterClient.SayHelloAsync` 호출 하 여 서버에 대 한 grpc 호출을 시작 합니다.

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet)]

기본적으로 `<Protobuf>` 항목 그룹에 포함 된 각 *@no__t -1* 파일에 대해 서버 및 클라이언트 자산이 생성 됩니다. 서버 프로젝트 `GrpcServices` 에서 서버 자산만 생성 되도록 하기 위해 특성은로 `Server`설정 됩니다.

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=2&range=7-9)]

마찬가지로, 특성은 클라이언트 프로젝트에서 `Client` 로 설정 됩니다.

[!INCLUDE[](~/includes/gRPCazure.md)]

## <a name="additional-resources"></a>추가 자료

* <xref:grpc/index>
* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/aspnetcore>
* <xref:grpc/client>
