---
title: C#을 사용하는 gRPC 서비스
author: juntaoluo
description: C#으로 gRPC 서비스를 작성하는 경우의 기본 개념을 알아봅니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 07/03/2019
uid: grpc/basics
ms.openlocfilehash: 59257449e211ddf9c7faa5f21a3986caba4eebc6
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78649425"
---
# <a name="grpc-services-with-c"></a><span data-ttu-id="2613a-103">C\#을 사용하는 gRPC 서비스</span><span class="sxs-lookup"><span data-stu-id="2613a-103">gRPC services with C\#</span></span>

<span data-ttu-id="2613a-104">이 문서에서는 C#으로 [gRPC](https://grpc.io/docs/guides/) 앱을 작성하는 데 필요한 개념을 간략하게 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="2613a-104">This document outlines the concepts needed to write [gRPC](https://grpc.io/docs/guides/) apps in C#.</span></span> <span data-ttu-id="2613a-105">여기에서 설명하는 내용은 [C-core](https://grpc.io/blog/grpc-stacks) 기반 gRPC 앱과 ASP.NET Core 기반 gRPC 앱에 모두 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="2613a-105">The topics covered here apply to both [C-core](https://grpc.io/blog/grpc-stacks)-based and ASP.NET Core-based gRPC apps.</span></span>

## <a name="proto-file"></a><span data-ttu-id="2613a-106">proto 파일</span><span class="sxs-lookup"><span data-stu-id="2613a-106">proto file</span></span>

<span data-ttu-id="2613a-107">gRPC는 API 개발에 계약 중심 접근 방식을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="2613a-107">gRPC uses a contract-first approach to API development.</span></span> <span data-ttu-id="2613a-108">기본적으로 프로토콜 버퍼(protobuf)가 IDL(Interface Design Language)로 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="2613a-108">Protocol buffers (protobuf) are used as the Interface Design Language (IDL) by default.</span></span> <span data-ttu-id="2613a-109">\* *.proto* 파일에는 다음이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="2613a-109">The *\*.proto* file contains:</span></span>

* <span data-ttu-id="2613a-110">gRPC 서비스 정의</span><span class="sxs-lookup"><span data-stu-id="2613a-110">The definition of the gRPC service.</span></span>
* <span data-ttu-id="2613a-111">클라이언트와 서버 간에 전송된 메시지</span><span class="sxs-lookup"><span data-stu-id="2613a-111">The messages sent between clients and servers.</span></span>

<span data-ttu-id="2613a-112">protobuf 파일 구문에 대한 자세한 내용은 [공식 문서](https://developers.google.com/protocol-buffers/docs/proto3)(protobuf)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="2613a-112">For more information on the syntax of protobuf files, see the [official documentation (protobuf)](https://developers.google.com/protocol-buffers/docs/proto3).</span></span>

<span data-ttu-id="2613a-113">예를 들어 [gRPC 서비스 시작](xref:tutorials/grpc/grpc-start)에서 사용된 *greet.proto* 파일의 내용은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="2613a-113">For example, consider the *greet.proto* file used in [Get started with gRPC service](xref:tutorials/grpc/grpc-start):</span></span>

* <span data-ttu-id="2613a-114">`Greeter` 서비스를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="2613a-114">Defines a `Greeter` service.</span></span>
* <span data-ttu-id="2613a-115">`Greeter` 서비스가 `SayHello` 호출을 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="2613a-115">The `Greeter` service defines a `SayHello` call.</span></span>
* <span data-ttu-id="2613a-116">`SayHello`가 `HelloRequest` 메시지를 보내고 `HelloReply` 메시지를 받습니다.</span><span class="sxs-lookup"><span data-stu-id="2613a-116">`SayHello` sends a `HelloRequest` message and receives a `HelloReply` message:</span></span>

[!code-protobuf[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Protos/greet.proto)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

## <a name="add-a-proto-file-to-a-c-app"></a><span data-ttu-id="2613a-117">C\# 앱에 .proto 파일 추가</span><span class="sxs-lookup"><span data-stu-id="2613a-117">Add a .proto file to a C\# app</span></span>

<span data-ttu-id="2613a-118">\* *.proto* 파일을 `<Protobuf>` 항목 그룹에 추가하여 프로젝트에 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="2613a-118">The *\*.proto* file is included in a project by adding it to the `<Protobuf>` item group:</span></span>

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=2&range=7-9)]

## <a name="c-tooling-support-for-proto-files"></a><span data-ttu-id="2613a-119">.proto 파일에 대한 C# 도구 지원</span><span class="sxs-lookup"><span data-stu-id="2613a-119">C# Tooling support for .proto files</span></span>

<span data-ttu-id="2613a-120">*\*.proto* 파일에서 C# 자산을 생성하려면 도구 패키지 [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/)가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="2613a-120">The tooling package [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/) is required to generate the C# assets from *\*.proto* files.</span></span> <span data-ttu-id="2613a-121">생성된 자산(파일)과 관련해서 다음 사항을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="2613a-121">The generated assets (files):</span></span>

* <span data-ttu-id="2613a-122">프로젝트를 빌드할 때마다 필요에 따라 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="2613a-122">Are generated on an as-needed basis each time the project is built.</span></span>
* <span data-ttu-id="2613a-123">프로젝트에 추가되거나 소스 제어에 체크 인되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="2613a-123">Aren't added to the project or checked into source control.</span></span>
* <span data-ttu-id="2613a-124">*obj* 디렉터리에 포함된 빌드 아티팩트입니다.</span><span class="sxs-lookup"><span data-stu-id="2613a-124">Are a build artifact contained in the *obj* directory.</span></span>

<span data-ttu-id="2613a-125">이 패키지는 서버 프로젝트와 클라이언트 프로젝트에서 모두 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="2613a-125">This package is required by both the server and client projects.</span></span> <span data-ttu-id="2613a-126">`Grpc.AspNetCore` 메타패키지에는 `Grpc.Tools`에 대한 참조가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="2613a-126">The `Grpc.AspNetCore` metapackage includes a reference to `Grpc.Tools`.</span></span> <span data-ttu-id="2613a-127">서버 프로젝트는 Visual Studio의 패키지 관리자를 사용하거나 프로젝트 파일에 `<PackageReference>`를 추가하여 `Grpc.AspNetCore`를 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2613a-127">Server projects can add `Grpc.AspNetCore` using the Package Manager in Visual Studio or by adding a `<PackageReference>` to the project file:</span></span>

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=1&range=12)]

<span data-ttu-id="2613a-128">클라이언트 프로젝트는 gRPC 클라이언트를 사용하는 데 필요한 다른 패키지와 함께 `Grpc.Tools`를 직접 참조해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="2613a-128">Client projects should directly reference `Grpc.Tools` alongside the other packages required to use the gRPC client.</span></span> <span data-ttu-id="2613a-129">도구 패키지는 런타임에 필요하지 않으므로 종속성이 `PrivateAssets="All"`로 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="2613a-129">The tooling package isn't required at runtime, so the dependency is marked with `PrivateAssets="All"`:</span></span>

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/GrpcGreeterClient.csproj?highlight=3&range=9-11)]

## <a name="generated-c-assets"></a><span data-ttu-id="2613a-130">생성된 C# 자산</span><span class="sxs-lookup"><span data-stu-id="2613a-130">Generated C# assets</span></span>

<span data-ttu-id="2613a-131">도구 패키지는 포함된 *\*.proto* 파일에서 정의된 메시지를 나타내는 C# 형식을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="2613a-131">The tooling package generates the C# types representing the messages defined in the included *\*.proto* files.</span></span>

<span data-ttu-id="2613a-132">서버 쪽 자산의 경우, 추상 서비스 기본 형식이 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="2613a-132">For server-side assets, an abstract service base type is generated.</span></span> <span data-ttu-id="2613a-133">기본 형식은 *.proto* 파일에 포함된 모든 gRPC 호출의 정의를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="2613a-133">The base type contains the definitions of all the gRPC calls contained in the *.proto* file.</span></span> <span data-ttu-id="2613a-134">이 기본 형식에서 파생되고 gRPC 호출의 논리를 구현하는 구체적인 서비스 구현을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="2613a-134">Create a concrete service implementation that derives from this base type and implements the logic for the gRPC calls.</span></span> <span data-ttu-id="2613a-135">앞에서 설명한 예제인 `greet.proto`의 경우, 가상 `SayHello` 메서드를 포함하는 추상 `GreeterBase` 형식이 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="2613a-135">For the `greet.proto`, the example described previously, an abstract `GreeterBase` type that contains a virtual `SayHello` method is generated.</span></span> <span data-ttu-id="2613a-136">구체적인 구현 `GreeterService`는 메서드를 재정의하고, gRPC 호출을 처리하는 논리를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="2613a-136">A concrete implementation `GreeterService` overrides the method and implements the logic handling the gRPC call.</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Services/GreeterService.cs?name=snippet)]

<span data-ttu-id="2613a-137">클라이언트 쪽 자산의 경우, 구체적인 클라이언트 형식이 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="2613a-137">For client-side assets, a concrete client type is generated.</span></span> <span data-ttu-id="2613a-138">*.proto* 파일의 gRPC 호출은 호출할 수 있는 구체적인 형식의 메서드로 변환됩니다.</span><span class="sxs-lookup"><span data-stu-id="2613a-138">The gRPC calls in the *.proto* file are translated into methods on the concrete type, which can be called.</span></span> <span data-ttu-id="2613a-139">앞에서 설명한 예제인 `greet.proto`의 경우, 구체적인 `GreeterClient` 형식이 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="2613a-139">For the `greet.proto`, the example described previously, a concrete `GreeterClient` type is generated.</span></span> <span data-ttu-id="2613a-140">`GreeterClient.SayHelloAsync`를 호출하여 서버에 대한 gRPC 호출을 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="2613a-140">Call `GreeterClient.SayHelloAsync` to initiate a gRPC call to the server.</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet)]

<span data-ttu-id="2613a-141">기본적으로 `<Protobuf>` 항목 그룹에 포함된 각 *\*.proto* 파일에 대해 서버 자산과 클라이언트 자산이 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="2613a-141">By default, server and client assets are generated for each *\*.proto* file included in the `<Protobuf>` item group.</span></span> <span data-ttu-id="2613a-142">서버 프로젝트에서 서버 자산만 생성되도록 하려면 `GrpcServices` 특성을 `Server`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="2613a-142">To ensure only the server assets are generated in a server project, the `GrpcServices` attribute is set to `Server`.</span></span>

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=2&range=7-9)]

<span data-ttu-id="2613a-143">마찬가지로, 클라이언트 프로젝트에서는 특성을 `Client`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="2613a-143">Similarly, the attribute is set to `Client` in client projects.</span></span>

[!INCLUDE[](~/includes/gRPCazure.md)]

## <a name="additional-resources"></a><span data-ttu-id="2613a-144">추가 자료</span><span class="sxs-lookup"><span data-stu-id="2613a-144">Additional resources</span></span>

* <xref:grpc/index>
* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/aspnetcore>
* <xref:grpc/client>
