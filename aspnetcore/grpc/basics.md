---
title: C#을 사용하는 gRPC 서비스
author: juntaoluo
description: 를 사용 하 여 gRPC 서비스를 C#작성할 때 기본 개념을 알아보세요.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 07/03/2019
uid: grpc/basics
ms.openlocfilehash: b236fe6914cf7b780a9d02398ec9c92660dc1063
ms.sourcegitcommit: 2719c70cd15a430479ab4007ff3e197fbf5dfee0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/09/2019
ms.locfileid: "68862852"
---
# <a name="grpc-services-with-c"></a><span data-ttu-id="39f73-103">C를 사용 하는 gRPC 서비스\#</span><span class="sxs-lookup"><span data-stu-id="39f73-103">gRPC services with C\#</span></span>

<span data-ttu-id="39f73-104">이 문서에서는에서 C# [grpc](https://grpc.io/docs/guides/) 앱을 작성 하는 데 필요한 개념을 간략하게 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="39f73-104">This document outlines the concepts needed to write [gRPC](https://grpc.io/docs/guides/) apps in C#.</span></span> <span data-ttu-id="39f73-105">여기에서 설명 하는 항목은 [C 코어](https://grpc.io/blog/grpc-stacks)기반 및 ASP.NET Core 기반 grpc 앱 모두에 적용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="39f73-105">The topics covered here apply to both [C-core](https://grpc.io/blog/grpc-stacks)-based and ASP.NET Core-based gRPC apps.</span></span>

## <a name="proto-file"></a><span data-ttu-id="39f73-106">프로토콜 파일</span><span class="sxs-lookup"><span data-stu-id="39f73-106">proto file</span></span>

<span data-ttu-id="39f73-107">gRPC는 계약 중심의 API 개발 방법을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="39f73-107">gRPC uses a contract-first approach to API development.</span></span> <span data-ttu-id="39f73-108">프로토콜 버퍼 (protobuf)는 기본적으로 IDL (인터페이스 디자인 언어)로 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="39f73-108">Protocol buffers (protobuf) are used as the Interface Design Language (IDL) by default.</span></span> <span data-ttu-id="39f73-109">이 파일에는 다음이 포함 *됩니다* .</span><span class="sxs-lookup"><span data-stu-id="39f73-109">The *.proto* file contains:</span></span>

* <span data-ttu-id="39f73-110">GRPC 서비스의 정의입니다.</span><span class="sxs-lookup"><span data-stu-id="39f73-110">The definition of the gRPC service.</span></span>
* <span data-ttu-id="39f73-111">클라이언트와 서버 간에 전송 되는 메시지입니다.</span><span class="sxs-lookup"><span data-stu-id="39f73-111">The messages sent between clients and servers.</span></span>

<span data-ttu-id="39f73-112">Protobuf 파일의 구문에 대 한 자세한 내용은 [공식 설명서 (protobuf)](https://developers.google.com/protocol-buffers/docs/proto3)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="39f73-112">For more information on the syntax of protobuf files, see the [official documentation (protobuf)](https://developers.google.com/protocol-buffers/docs/proto3).</span></span>

<span data-ttu-id="39f73-113">예를 들어 [gRPC 서비스 시작](xref:tutorials/grpc/grpc-start)에 사용 되는 *인사* 파일을 사용 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="39f73-113">For example, consider the *greet.proto* file used in [Get started with gRPC service](xref:tutorials/grpc/grpc-start):</span></span>

* <span data-ttu-id="39f73-114">서비스를 `Greeter` 정의 합니다.</span><span class="sxs-lookup"><span data-stu-id="39f73-114">Defines a `Greeter` service.</span></span>
* <span data-ttu-id="39f73-115">서비스 `Greeter` 는 호출을 `SayHello` 정의 합니다.</span><span class="sxs-lookup"><span data-stu-id="39f73-115">The `Greeter` service defines a `SayHello` call.</span></span>
* <span data-ttu-id="39f73-116">`SayHello`메시지를 `HelloRequest` 보내고 메시지를 `HelloReply` 받습니다.</span><span class="sxs-lookup"><span data-stu-id="39f73-116">`SayHello` sends a `HelloRequest` message and receives a `HelloReply` message:</span></span>

[!code-protobuf[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Protos/greet.proto)]

## <a name="add-a-proto-file-to-a-c-app"></a><span data-ttu-id="39f73-117">C\# 앱에 proto 파일 추가</span><span class="sxs-lookup"><span data-stu-id="39f73-117">Add a .proto file to a C\# app</span></span>

<span data-ttu-id="39f73-118">이 파일은 `<Protobuf>` 항목 그룹에 추가 하 여 프로젝트에 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="39f73-118">The *.proto* file is included in a project by adding it to the `<Protobuf>` item group:</span></span>

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=2&range=7-9)]

## <a name="c-tooling-support-for-proto-files"></a><span data-ttu-id="39f73-119">C#Proto 파일에 대 한 도구 지원</span><span class="sxs-lookup"><span data-stu-id="39f73-119">C# Tooling support for .proto files</span></span>

<span data-ttu-id="39f73-120">[도구 패키지 Grpc](https://www.nuget.org/packages/Grpc.Tools/) 는 .pfiles에서 자산을 C# 생성 하는 데 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="39f73-120">The tooling package [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/) is required to generate the C# assets from *.proto* files.</span></span> <span data-ttu-id="39f73-121">생성 된 자산 (파일):</span><span class="sxs-lookup"><span data-stu-id="39f73-121">The generated assets (files):</span></span>

* <span data-ttu-id="39f73-122">는 프로젝트가 빌드될 때마다 필요에 따라 생성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="39f73-122">Are generated on an as-needed basis each time the project is built.</span></span>
* <span data-ttu-id="39f73-123">프로젝트에 추가 되거나 소스 제어에 체크 인 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="39f73-123">Aren't added to the project or checked into source control.</span></span>
* <span data-ttu-id="39f73-124">는 *obj* 디렉터리에 포함 된 빌드 아티팩트입니다.</span><span class="sxs-lookup"><span data-stu-id="39f73-124">Are a build artifact contained in the *obj* directory.</span></span>

<span data-ttu-id="39f73-125">이 패키지는 서버 및 클라이언트 프로젝트 모두에 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="39f73-125">This package is required by both the server and client projects.</span></span> <span data-ttu-id="39f73-126">메타 패키지에는에 대 `Grpc.Tools`한 참조가 포함 되어 있습니다. `Grpc.AspNetCore`</span><span class="sxs-lookup"><span data-stu-id="39f73-126">The `Grpc.AspNetCore` metapackage includes a reference to `Grpc.Tools`.</span></span> <span data-ttu-id="39f73-127">서버 프로젝트는 Visual `Grpc.AspNetCore` Studio의 패키지 관리자를 사용 하거나 프로젝트 파일에를 `<PackageReference>` 추가 하 여 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="39f73-127">Server projects can add `Grpc.AspNetCore` using the Package Manager in Visual Studio or by adding a `<PackageReference>` to the project file:</span></span>

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=1&range=12)]

<span data-ttu-id="39f73-128">클라이언트 프로젝트는 grpc `Grpc.Tools` 클라이언트를 사용 하는 데 필요한 다른 패키지와 함께 직접 참조 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="39f73-128">Client projects should directly reference `Grpc.Tools` alongside the other packages required to use the gRPC client.</span></span> <span data-ttu-id="39f73-129">도구 패키지는 런타임에 필요 하지 않으므로 종속성은 `PrivateAssets="All"`다음과 같이 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="39f73-129">The tooling package isn't required at runtime, so the dependency is marked with `PrivateAssets="All"`:</span></span>

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/GrpcGreeterClient.csproj?highlight=3&range=9-11)]

## <a name="generated-c-assets"></a><span data-ttu-id="39f73-130">생성 C# 된 자산</span><span class="sxs-lookup"><span data-stu-id="39f73-130">Generated C# assets</span></span>

<span data-ttu-id="39f73-131">도구 패키지는 포함 된 C# *proto* 파일에 정의 된 메시지를 나타내는 형식을 생성 합니다.</span><span class="sxs-lookup"><span data-stu-id="39f73-131">The tooling package generates the C# types representing the messages defined in the included *.proto* files.</span></span>

<span data-ttu-id="39f73-132">서버 쪽 자산의 경우 추상 서비스 기본 유형이 생성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="39f73-132">For server-side assets, an abstract service base type is generated.</span></span> <span data-ttu-id="39f73-133">기본 형식에는 .pa 파일에 포함 된 모든 gRPC 호출의 정의가 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="39f73-133">The base type contains the definitions of all the gRPC calls contained in the *.proto* file.</span></span> <span data-ttu-id="39f73-134">이 기본 형식에서 파생 되 고 gRPC 호출에 대 한 논리를 구현 하는 구체적 서비스 구현을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="39f73-134">Create a concrete service implementation that derives from this base type and implements the logic for the gRPC calls.</span></span> <span data-ttu-id="39f73-135">에 대해 앞에서 설명한 예제에는 가상 `SayHello` 메서드 `GreeterBase` 를 포함 하는 추상 형식이 생성 됩니다. `greet.proto`</span><span class="sxs-lookup"><span data-stu-id="39f73-135">For the `greet.proto`, the example described previously, an abstract `GreeterBase` type that contains a virtual `SayHello` method is generated.</span></span> <span data-ttu-id="39f73-136">구체적 구현은 `GreeterService` 메서드를 재정의 하 고 grpc 호출을 처리 하는 논리를 구현 합니다.</span><span class="sxs-lookup"><span data-stu-id="39f73-136">A concrete implementation `GreeterService` overrides the method and implements the logic handling the gRPC call.</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Services/GreeterService.cs?name=snippet)]

<span data-ttu-id="39f73-137">클라이언트 쪽 자산의 경우 구체적인 클라이언트 형식이 생성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="39f73-137">For client-side assets, a concrete client type is generated.</span></span> <span data-ttu-id="39f73-138">.Pa 파일의 gRPC 호출은 구체적 형식에서 메서드로 변환 되며이를 호출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="39f73-138">The gRPC calls in the *.proto* file are translated into methods on the concrete type, which can be called.</span></span> <span data-ttu-id="39f73-139">에 대해 앞에서 설명한 예제는 구체적 `GreeterClient` 형식이 생성 됩니다. `greet.proto`</span><span class="sxs-lookup"><span data-stu-id="39f73-139">For the `greet.proto`, the example described previously, a concrete `GreeterClient` type is generated.</span></span> <span data-ttu-id="39f73-140">를 `GreeterClient.SayHelloAsync` 호출 하 여 서버에 대 한 grpc 호출을 시작 합니다.</span><span class="sxs-lookup"><span data-stu-id="39f73-140">Call `GreeterClient.SayHelloAsync` to initiate a gRPC call to the server.</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?highlight=3-6&name=snippet)]

<span data-ttu-id="39f73-141">기본적으로 서버 및 클라이언트 자산은 `<Protobuf>` 항목 그룹에 포함 된 각 파일에 대해 생성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="39f73-141">By default, server and client assets are generated for each *.proto* file included in the `<Protobuf>` item group.</span></span> <span data-ttu-id="39f73-142">서버 프로젝트 `GrpcServices` 에서 서버 자산만 생성 되도록 하기 위해 특성은로 `Server`설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="39f73-142">To ensure only the server assets are generated in a server project, the `GrpcServices` attribute is set to `Server`.</span></span>

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=2&range=7-9)]

<span data-ttu-id="39f73-143">마찬가지로, 특성은 클라이언트 프로젝트에서 `Client` 로 설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="39f73-143">Similarly, the attribute is set to `Client` in client projects.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="39f73-144">추가 자료</span><span class="sxs-lookup"><span data-stu-id="39f73-144">Additional resources</span></span>

* <xref:grpc/index>
* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/aspnetcore>
* <xref:grpc/migration>
