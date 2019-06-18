---
title: C#을 사용하는 gRPC 서비스
author: juntaoluo
description: GRPC 서비스를 작성할 때 기본 개념을 알아보고 C#입니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 03/31/2019
uid: grpc/basics
ms.openlocfilehash: 00772144cb484b78a256f178642463577d316be2
ms.sourcegitcommit: 516f166c5f7cec54edf3d9c71e6e2ba53fb3b0e5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67196355"
---
# <a name="grpc-services-with-c"></a><span data-ttu-id="2f96b-103">C 사용 하 여 gRPC 서비스\#</span><span class="sxs-lookup"><span data-stu-id="2f96b-103">gRPC services with C\#</span></span>

<span data-ttu-id="2f96b-104">이 문서를 작성 하는 데 필요한 개념에 간략하게 설명 [gRPC](https://grpc.io/docs/guides/) 앱에서 C#합니다.</span><span class="sxs-lookup"><span data-stu-id="2f96b-104">This document outlines the concepts needed to write [gRPC](https://grpc.io/docs/guides/) apps in C#.</span></span> <span data-ttu-id="2f96b-105">여기서 다루는 항목 둘 다에 적용 [C-core](https://grpc.io/blog/grpc-stacks)-gRPC 기반 및 ASP.NET Core 기반 앱.</span><span class="sxs-lookup"><span data-stu-id="2f96b-105">The topics covered here apply to both [C-core](https://grpc.io/blog/grpc-stacks)-based and ASP.NET Core-based gRPC apps.</span></span>

## <a name="proto-file"></a><span data-ttu-id="2f96b-106">proto 파일</span><span class="sxs-lookup"><span data-stu-id="2f96b-106">proto file</span></span>

<span data-ttu-id="2f96b-107">gRPC는 API 개발에는 계약 중심 접근 방식을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="2f96b-107">gRPC uses a contract-first approach to API development.</span></span> <span data-ttu-id="2f96b-108">프로토콜 버퍼 (protobuf)는 기본적으로 디자인 언어 IDL (Interface)를 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2f96b-108">Protocol buffers (protobuf) are used as the Interface Design Language (IDL) by default.</span></span> <span data-ttu-id="2f96b-109">합니다 *.proto* 파일 포함:</span><span class="sxs-lookup"><span data-stu-id="2f96b-109">The *.proto* file contains:</span></span>

* <span data-ttu-id="2f96b-110">GRPC 서비스의 정의입니다.</span><span class="sxs-lookup"><span data-stu-id="2f96b-110">The definition of the gRPC service.</span></span>
* <span data-ttu-id="2f96b-111">클라이언트와 서버 간에 전송 된 메시지입니다.</span><span class="sxs-lookup"><span data-stu-id="2f96b-111">The messages sent between clients and servers.</span></span>

<span data-ttu-id="2f96b-112">Protobuf 파일의 구문에 자세한 내용은 참조는 [공식 설명서 (protobuf)](https://developers.google.com/protocol-buffers/docs/proto3)합니다.</span><span class="sxs-lookup"><span data-stu-id="2f96b-112">For more information on the syntax of protobuf files, see the [official documentation (protobuf)](https://developers.google.com/protocol-buffers/docs/proto3).</span></span>

<span data-ttu-id="2f96b-113">예를 들어 합니다 *greet.proto* 에서 사용 되는 파일 [gRPC service 시작](xref:tutorials/grpc/grpc-start):</span><span class="sxs-lookup"><span data-stu-id="2f96b-113">For example, consider the *greet.proto* file used in [Get started with gRPC service](xref:tutorials/grpc/grpc-start):</span></span>

* <span data-ttu-id="2f96b-114">정의 `Greeter` 서비스입니다.</span><span class="sxs-lookup"><span data-stu-id="2f96b-114">Defines a `Greeter` service.</span></span>
* <span data-ttu-id="2f96b-115">합니다 `Greeter` 서비스 정의 `SayHello` 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="2f96b-115">The `Greeter` service defines a `SayHello` call.</span></span>
* <span data-ttu-id="2f96b-116">`SayHello` 전송 된 `HelloRequest` 받고 메시지는 `HelloResponse` 메시지:</span><span class="sxs-lookup"><span data-stu-id="2f96b-116">`SayHello` sends a `HelloRequest` message and receives a `HelloResponse` message:</span></span>

[!code-proto[](~/tutorials//grpc/grpc-start/sample/GrpcGreeter/Protos/greet.proto)]

## <a name="add-a-proto-file-to-a-c-app"></a><span data-ttu-id="2f96b-117">C.proto 파일로 추가할\# 앱</span><span class="sxs-lookup"><span data-stu-id="2f96b-117">Add a .proto file to a C\# app</span></span>

<span data-ttu-id="2f96b-118">합니다 *.proto* 파일을 프로젝트에 추가 하 여 포함 된 `<Protobuf>` 항목 그룹:</span><span class="sxs-lookup"><span data-stu-id="2f96b-118">The *.proto* file is included in a project by adding it to the `<Protobuf>` item group:</span></span>

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=2&range=7-9)]

## <a name="c-tooling-support-for-proto-files"></a><span data-ttu-id="2f96b-119">C#도구.proto 파일에 대 한 지원</span><span class="sxs-lookup"><span data-stu-id="2f96b-119">C# Tooling support for .proto files</span></span>

<span data-ttu-id="2f96b-120">도구 패키지 [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/) 생성 하는 데 필요한 합니다 C# 에서 자산 *.proto* 파일입니다.</span><span class="sxs-lookup"><span data-stu-id="2f96b-120">The tooling package [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/) is required to generate the C# assets from *.proto* files.</span></span> <span data-ttu-id="2f96b-121">생성 된 자산 (파일):</span><span class="sxs-lookup"><span data-stu-id="2f96b-121">The generated assets (files):</span></span>

* <span data-ttu-id="2f96b-122">에 생성 됩니다는 필요에 따라 프로젝트를 빌드할 때마다.</span><span class="sxs-lookup"><span data-stu-id="2f96b-122">Are generated on an as-needed basis each time the project is built.</span></span>
* <span data-ttu-id="2f96b-123">프로젝트에 추가 하거나 원본 제어에 체크 인 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="2f96b-123">Aren't added to the project or checked into source control.</span></span>
* <span data-ttu-id="2f96b-124">에 포함 된 빌드 아티팩트를은 *obj* 디렉터리입니다.</span><span class="sxs-lookup"><span data-stu-id="2f96b-124">Are a build artifact contained in the *obj* directory.</span></span>

<span data-ttu-id="2f96b-125">서버 및 클라이언트 프로젝트에서이 패키지가 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="2f96b-125">This package is required by both the server and client projects.</span></span> <span data-ttu-id="2f96b-126">`Grpc.Tools` Visual Studio에서 패키지 관리자를 사용 하거나 추가 하 여 추가할 수는 `<PackageReference>` 프로젝트 파일에:</span><span class="sxs-lookup"><span data-stu-id="2f96b-126">`Grpc.Tools` can be added by using the Package Manager in Visual Studio or adding a `<PackageReference>` to the project file:</span></span>

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=1&range=15)]

<span data-ttu-id="2f96b-127">도구 패키지는 런타임에 필요하지 않으므로 종속성은 `PrivateAssets="All"`로 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="2f96b-127">The tooling package isn't required at runtime, so the dependency is marked with `PrivateAssets="All"`.</span></span>

## <a name="generated-c-assets"></a><span data-ttu-id="2f96b-128">생성 된 C# 자산</span><span class="sxs-lookup"><span data-stu-id="2f96b-128">Generated C# assets</span></span>

<span data-ttu-id="2f96b-129">도구 패키지를 생성 합니다 C# 형식 정의에 포함 된 메시지를 나타내는 *.proto* 파일입니다.</span><span class="sxs-lookup"><span data-stu-id="2f96b-129">The tooling package generates the C# types representing the messages defined in the included *.proto* files.</span></span>

<span data-ttu-id="2f96b-130">서버 쪽 자산에 대 한 추상 서비스 기본 형식에는 생성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2f96b-130">For server-side assets, an abstract service base type is generated.</span></span> <span data-ttu-id="2f96b-131">기본 형식 gRPC 호출에 포함 된 모든 정의 포함 합니다 *.proto* 파일입니다.</span><span class="sxs-lookup"><span data-stu-id="2f96b-131">The base type contains the definitions of all the gRPC calls contained in the *.proto* file.</span></span> <span data-ttu-id="2f96b-132">이 기본 형식에서 파생 되며 gRPC 호출에 대 한 논리를 구현 하는 구체적인 서비스 구현을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="2f96b-132">Create a concrete service implementation that derives from this base type and implements the logic for the gRPC calls.</span></span> <span data-ttu-id="2f96b-133">에 대 한 합니다 `greet.proto`,이 예제에서는 앞에서 설명한 추상 `GreeterBase` 가상 포함 하는 형식 `SayHello` 메서드가 생성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2f96b-133">For the `greet.proto`, the example described previously, an abstract `GreeterBase` type that contains a virtual `SayHello` method is generated.</span></span> <span data-ttu-id="2f96b-134">구체적인 구현을 `GreeterService` 메서드를 재정의 하 고 gRPC 호출을 처리 하는 논리를 구현 합니다.</span><span class="sxs-lookup"><span data-stu-id="2f96b-134">A concrete implementation `GreeterService` overrides the method and implements the logic handling the gRPC call.</span></span>

[!code-csharp[](~/tutorials//grpc/grpc-start/sample/GrpcGreeter/Services/GreeterService.cs?name=snippet)]

<span data-ttu-id="2f96b-135">클라이언트 쪽 자산에 대 한 구체적인 클라이언트 형식을 생성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2f96b-135">For client-side assets, a concrete client type is generated.</span></span> <span data-ttu-id="2f96b-136">는 gRPC를 호출 합니다 *.proto* 파일 메서드를 호출할 수 있는 구체적인 형식으로 변환 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2f96b-136">The gRPC calls in the *.proto* file are translated into methods on the concrete type, which can be called.</span></span> <span data-ttu-id="2f96b-137">에 대 한는 `greet.proto`, 예제에서는 앞에서 설명한 구체적인 `GreeterClient` 형식이 생성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2f96b-137">For the `greet.proto`, the example described previously, a concrete `GreeterClient` type is generated.</span></span> <span data-ttu-id="2f96b-138">호출 `GreeterClient.SayHello` 서버로 gRPC 호출을 시작 합니다.</span><span class="sxs-lookup"><span data-stu-id="2f96b-138">Call `GreeterClient.SayHello` to initiate a gRPC call to the server.</span></span>

[!code-csharp[](~/tutorials//grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?highlight=5-8&name=snippet)]

<span data-ttu-id="2f96b-139">기본적으로 서버 및 클라이언트 자산 각각에 대해 생성 됩니다 *.proto* 에 포함 된 파일을 `<Protobuf>` 항목 그룹입니다.</span><span class="sxs-lookup"><span data-stu-id="2f96b-139">By default, server and client assets are generated for each *.proto* file included in the `<Protobuf>` item group.</span></span> <span data-ttu-id="2f96b-140">서버 자산에만 서버 프로젝트에서 생성 되는 `GrpcServices` 특성이로 설정 된 `Server`합니다.</span><span class="sxs-lookup"><span data-stu-id="2f96b-140">To ensure only the server assets are generated in a server project, the `GrpcServices` attribute is set to `Server`.</span></span>

[!code-xml[](~/tutorials//grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=2&range=7-9)]

<span data-ttu-id="2f96b-141">특성이로 설정 되는 마찬가지로 `Client` 클라이언트 프로젝트에서.</span><span class="sxs-lookup"><span data-stu-id="2f96b-141">Similarly, the attribute is set to `Client` in client projects.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="2f96b-142">추가 자료</span><span class="sxs-lookup"><span data-stu-id="2f96b-142">Additional resources</span></span>

* <xref:grpc/index>
* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/aspnetcore>
* <xref:grpc/migration>
