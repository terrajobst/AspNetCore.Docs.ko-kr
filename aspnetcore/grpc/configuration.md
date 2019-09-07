---
title: ASP.NET Core 구성을 위한 gRPC
author: jamesnk
description: ASP.NET Core 앱에 대 한 gRPC를 구성 하는 방법에 대해 알아봅니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 09/05/2019
uid: grpc/configuration
ms.openlocfilehash: d6f095820271a3bb07e05e29299fbb82b042983b
ms.sourcegitcommit: f65d8765e4b7c894481db9b37aa6969abc625a48
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70773677"
---
# <a name="grpc-for-aspnet-core-configuration"></a><span data-ttu-id="21313-103">ASP.NET Core 구성을 위한 gRPC</span><span class="sxs-lookup"><span data-stu-id="21313-103">gRPC for ASP.NET Core configuration</span></span>

## <a name="configure-services-options"></a><span data-ttu-id="21313-104">서비스 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="21313-104">Configure services options</span></span>

<span data-ttu-id="21313-105">다음 표에서는 gRPC 서비스를 구성 하는 옵션을 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="21313-105">The following table describes options for configuring gRPC services:</span></span>

| <span data-ttu-id="21313-106">옵션</span><span class="sxs-lookup"><span data-stu-id="21313-106">Option</span></span> | <span data-ttu-id="21313-107">기본값</span><span class="sxs-lookup"><span data-stu-id="21313-107">Default Value</span></span> | <span data-ttu-id="21313-108">설명</span><span class="sxs-lookup"><span data-stu-id="21313-108">Description</span></span> |
| ------ | ------------- | ----------- |
| `MaxSendMessageSize` | `null` | <span data-ttu-id="21313-109">서버에서 보낼 수 있는 최대 메시지 크기 (바이트)입니다.</span><span class="sxs-lookup"><span data-stu-id="21313-109">The maximum message size in bytes that can be sent from the server.</span></span> <span data-ttu-id="21313-110">구성 된 최대 메시지 크기를 초과 하는 메시지를 전송 하려고 하면 예외가 발생 합니다.</span><span class="sxs-lookup"><span data-stu-id="21313-110">Attempting to send a message that exceeds the configured maximum message size results in an exception.</span></span> |
| `MaxReceiveMessageSize` | <span data-ttu-id="21313-111">4MB</span><span class="sxs-lookup"><span data-stu-id="21313-111">4 MB</span></span> | <span data-ttu-id="21313-112">서버에서 받을 수 있는 최대 메시지 크기 (바이트)입니다.</span><span class="sxs-lookup"><span data-stu-id="21313-112">The maximum message size in bytes that can be received by the server.</span></span> <span data-ttu-id="21313-113">서버에서이 한도를 초과 하는 메시지를 수신 하는 경우 예외가 throw 됩니다.</span><span class="sxs-lookup"><span data-stu-id="21313-113">If the server receives a message that exceeds this limit, it throws an exception.</span></span> <span data-ttu-id="21313-114">이 값을 늘리면 서버가 더 큰 메시지를 수신할 수 있지만 메모리 소비에 부정적인 영향을 줄 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="21313-114">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="21313-115">인 `true`경우 서비스 메서드에서 예외가 throw 되 면 자세한 예외 메시지가 클라이언트에 반환 됩니다.</span><span class="sxs-lookup"><span data-stu-id="21313-115">If `true`, detailed exception messages are returned to clients when an exception is thrown in a service method.</span></span> <span data-ttu-id="21313-116">기본값은 `false`입니다.</span><span class="sxs-lookup"><span data-stu-id="21313-116">The default is `false`.</span></span> <span data-ttu-id="21313-117">`EnableDetailedErrors` 로`true` 설정 하면 중요 한 정보가 누출 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="21313-117">Setting `EnableDetailedErrors` to `true` can leak sensitive information.</span></span> |
| `CompressionProviders` | <span data-ttu-id="21313-118">gzip</span><span class="sxs-lookup"><span data-stu-id="21313-118">gzip</span></span> | <span data-ttu-id="21313-119">메시지를 압축 하 고 압축을 푸는 데 사용 되는 압축 공급자의 컬렉션입니다.</span><span class="sxs-lookup"><span data-stu-id="21313-119">A collection of compression providers used to compress and decompress messages.</span></span> <span data-ttu-id="21313-120">사용자 지정 압축 공급자를 만들어 컬렉션에 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="21313-120">Custom compression providers can be created and added to the collection.</span></span> <span data-ttu-id="21313-121">구성 된 기본 공급자는 **gzip** 압축을 지원 합니다.</span><span class="sxs-lookup"><span data-stu-id="21313-121">The default configured providers support **gzip** compression.</span></span> |
| `ResponseCompressionAlgorithm` | `null` | <span data-ttu-id="21313-122">서버에서 보낸 메시지를 압축 하는 데 사용 되는 압축 알고리즘입니다.</span><span class="sxs-lookup"><span data-stu-id="21313-122">The compression algorithm used to compress messages sent from the server.</span></span> <span data-ttu-id="21313-123">알고리즘은의 `CompressionProviders`압축 공급자와 일치 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="21313-123">The algorithm must match a compression provider in `CompressionProviders`.</span></span> <span data-ttu-id="21313-124">응답을 압축 하는 알고리즘의 경우 클라이언트는 **grpc-수락-인코딩** 헤더에 알고리즘을 전송 하 여 알고리즘을 지원 하도록 지정 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="21313-124">For the algorithm to compress a response, the client must indicate it supports the algorithm by sending it in the **grpc-accept-encoding** header.</span></span> |
| `ResponseCompressionLevel` | `null` | <span data-ttu-id="21313-125">서버에서 보낸 메시지를 압축 하는 데 사용 되는 압축 수준입니다.</span><span class="sxs-lookup"><span data-stu-id="21313-125">The compress level used to compress messages sent from the server.</span></span> |

<span data-ttu-id="21313-126">`AddGrpc` 에서`Startup.ConfigureServices`호출에 대 한 옵션 대리자를 제공 하 여 모든 서비스에 대해 옵션을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="21313-126">Options can be configured for all services by providing an options delegate to the `AddGrpc` call in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](~/grpc/configuration/sample/GrcpService/Startup.cs?name=snippet)]

<span data-ttu-id="21313-127">단일 서비스에 대 한 옵션은에서 `AddGrpc` 제공 하는 전역 옵션을 재정의 하 고 다음을 사용 하 여 `AddServiceOptions<TService>`구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="21313-127">Options for a single service override the global options provided in `AddGrpc` and can be configured using `AddServiceOptions<TService>`:</span></span>

[!code-csharp[](~/grpc/configuration/sample/GrcpService/Startup2.cs?name=snippet)]

## <a name="configure-client-options"></a><span data-ttu-id="21313-128">클라이언트 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="21313-128">Configure client options</span></span>

<span data-ttu-id="21313-129">다음 표에서는 gRPC 채널을 구성 하는 옵션을 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="21313-129">The following table describes options for configuring gRPC channels:</span></span>

| <span data-ttu-id="21313-130">옵션</span><span class="sxs-lookup"><span data-stu-id="21313-130">Option</span></span> | <span data-ttu-id="21313-131">기본값</span><span class="sxs-lookup"><span data-stu-id="21313-131">Default Value</span></span> | <span data-ttu-id="21313-132">Description</span><span class="sxs-lookup"><span data-stu-id="21313-132">Description</span></span> |
| ------ | ------------- | ----------- |
| `HttpClient` | <span data-ttu-id="21313-133">새 인스턴스</span><span class="sxs-lookup"><span data-stu-id="21313-133">New instance</span></span> | <span data-ttu-id="21313-134">Grpc 호출을 수행 하는 데 사용 되는 `HttpClient` 입니다.</span><span class="sxs-lookup"><span data-stu-id="21313-134">The `HttpClient` used to make gRPC calls.</span></span> <span data-ttu-id="21313-135">클라이언트를 설정 하 여 사용자 지정 `HttpClientHandler`을 구성 하거나 grpc 호출에 대 한 HTTP 파이프라인에 추가 처리기를 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="21313-135">A client can be set to configure a custom `HttpClientHandler`, or add additional handlers to the HTTP pipeline for gRPC calls.</span></span> <span data-ttu-id="21313-136">을 지정 `HttpClient` 하지 않으면 채널에 대 한 `HttpClient` 새 인스턴스가 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="21313-136">If no `HttpClient` is specified, then a new `HttpClient` instance is created for the channel.</span></span> <span data-ttu-id="21313-137">자동으로 삭제 됩니다.</span><span class="sxs-lookup"><span data-stu-id="21313-137">It will automatically be disposed.</span></span> |
| `DisposeHttpClient` | `false` | <span data-ttu-id="21313-138">및가 지정 `true` `GrpcChannel` 된 경우이 삭제 되 면 인스턴스가삭제됩니다.`HttpClient` `HttpClient`</span><span class="sxs-lookup"><span data-stu-id="21313-138">If `true`, and an `HttpClient` is specified, then the `HttpClient` instance will be disposed when the `GrpcChannel` is disposed.</span></span> |
| `LoggerFactory` | `null` | <span data-ttu-id="21313-139">클라이언트에서 grpc 호출에 대 한 정보를 기록 하 는데사용하는입니다.`LoggerFactory`</span><span class="sxs-lookup"><span data-stu-id="21313-139">The `LoggerFactory` used by the client to log information about gRPC calls.</span></span> <span data-ttu-id="21313-140">인스턴스 `LoggerFactory` 는 종속성 주입에서 확인 하거나를 사용 하 여 `LoggerFactory.Create`만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="21313-140">A `LoggerFactory` instance can be resolved from dependency injection or created using `LoggerFactory.Create`.</span></span> <span data-ttu-id="21313-141">로깅 구성 예제는를 참조 <xref:fundamentals/logging/index>하십시오.</span><span class="sxs-lookup"><span data-stu-id="21313-141">For examples of configuring logging, see <xref:fundamentals/logging/index>.</span></span> |
| `MaxSendMessageSize` | `null` | <span data-ttu-id="21313-142">클라이언트에서 보낼 수 있는 최대 메시지 크기 (바이트)입니다.</span><span class="sxs-lookup"><span data-stu-id="21313-142">The maximum message size in bytes that can be sent from the client.</span></span> <span data-ttu-id="21313-143">구성 된 최대 메시지 크기를 초과 하는 메시지를 전송 하려고 하면 예외가 발생 합니다.</span><span class="sxs-lookup"><span data-stu-id="21313-143">Attempting to send a message that exceeds the configured maximum message size results in an exception.</span></span> |
| `MaxReceiveMessageSize` | <span data-ttu-id="21313-144">4MB</span><span class="sxs-lookup"><span data-stu-id="21313-144">4 MB</span></span> | <span data-ttu-id="21313-145">클라이언트에서 받을 수 있는 최대 메시지 크기 (바이트)입니다.</span><span class="sxs-lookup"><span data-stu-id="21313-145">The maximum message size in bytes that can be received by the client.</span></span> <span data-ttu-id="21313-146">클라이언트에서이 한도를 초과 하는 메시지를 수신 하면 예외가 throw 됩니다.</span><span class="sxs-lookup"><span data-stu-id="21313-146">If the client receives a message that exceeds this limit, it throws an exception.</span></span> <span data-ttu-id="21313-147">이 값을 늘리면 클라이언트는 더 큰 메시지를 받을 수 있지만 메모리 사용에 부정적인 영향을 줄 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="21313-147">Increasing this value allows the client to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `Credentials` | `null` | <span data-ttu-id="21313-148">`ChannelCredentials` 인스턴스입니다.</span><span class="sxs-lookup"><span data-stu-id="21313-148">A `ChannelCredentials` instance.</span></span> <span data-ttu-id="21313-149">자격 증명은 gRPC 호출에 인증 메타 데이터를 추가 하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="21313-149">Credentials are used to add authentication metadata to gRPC calls.</span></span> |
| `CompressionProviders` | <span data-ttu-id="21313-150">gzip</span><span class="sxs-lookup"><span data-stu-id="21313-150">gzip</span></span> | <span data-ttu-id="21313-151">메시지를 압축 하 고 압축을 푸는 데 사용 되는 압축 공급자의 컬렉션입니다.</span><span class="sxs-lookup"><span data-stu-id="21313-151">A collection of compression providers used to compress and decompress messages.</span></span> <span data-ttu-id="21313-152">사용자 지정 압축 공급자를 만들어 컬렉션에 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="21313-152">Custom compression providers can be created and added to the collection.</span></span> <span data-ttu-id="21313-153">구성 된 기본 공급자는 **gzip** 압축을 지원 합니다.</span><span class="sxs-lookup"><span data-stu-id="21313-153">The default configured providers support **gzip** compression.</span></span> |

<span data-ttu-id="21313-154">다음 예를 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="21313-154">The following code:</span></span>

* <span data-ttu-id="21313-155">채널의 최대 송신 및 수신 메시지 크기를 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="21313-155">Sets the maximum send and receive message size on the channel.</span></span>
* <span data-ttu-id="21313-156">클라이언트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="21313-156">Creates a client.</span></span>

[!code-csharp[](~/grpc/configuration/sample/Program.cs?name=snippet&highlight=3-8)]

## <a name="additional-resources"></a><span data-ttu-id="21313-157">추가 자료</span><span class="sxs-lookup"><span data-stu-id="21313-157">Additional resources</span></span>

* <xref:grpc/aspnetcore>
* <xref:grpc/client>
* <xref:tutorials/grpc/grpc-start>
