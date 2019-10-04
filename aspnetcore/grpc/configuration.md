---
title: .NET 용 gRPC 구성
author: jamesnk
description: .NET 앱 용 gRPC를 구성 하는 방법에 대해 알아봅니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 09/05/2019
uid: grpc/configuration
ms.openlocfilehash: 3ef92f10d914ef9fa3e13a7bdd5c863bab297f57
ms.sourcegitcommit: 73e255e846e414821b8cc20ffa3aec946735cd4e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/03/2019
ms.locfileid: "71925216"
---
# <a name="grpc-for-net-configuration"></a><span data-ttu-id="521f0-103">.NET 용 gRPC 구성</span><span class="sxs-lookup"><span data-stu-id="521f0-103">gRPC for .NET configuration</span></span>

## <a name="configure-services-options"></a><span data-ttu-id="521f0-104">서비스 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="521f0-104">Configure services options</span></span>

<span data-ttu-id="521f0-105">grpc 서비스는 *Startup.cs*에서 `AddGrpc` 로 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="521f0-105">gRPC services are configured with `AddGrpc` in *Startup.cs*.</span></span> <span data-ttu-id="521f0-106">다음 표에서는 gRPC 서비스를 구성 하는 옵션을 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="521f0-106">The following table describes options for configuring gRPC services:</span></span>

| <span data-ttu-id="521f0-107">옵션</span><span class="sxs-lookup"><span data-stu-id="521f0-107">Option</span></span> | <span data-ttu-id="521f0-108">Default Value</span><span class="sxs-lookup"><span data-stu-id="521f0-108">Default Value</span></span> | <span data-ttu-id="521f0-109">설명</span><span class="sxs-lookup"><span data-stu-id="521f0-109">Description</span></span> |
| ------ | ------------- | ----------- |
| `MaxSendMessageSize` | `null` | <span data-ttu-id="521f0-110">서버에서 보낼 수 있는 최대 메시지 크기 (바이트)입니다.</span><span class="sxs-lookup"><span data-stu-id="521f0-110">The maximum message size in bytes that can be sent from the server.</span></span> <span data-ttu-id="521f0-111">구성 된 최대 메시지 크기를 초과 하는 메시지를 전송 하려고 하면 예외가 발생 합니다.</span><span class="sxs-lookup"><span data-stu-id="521f0-111">Attempting to send a message that exceeds the configured maximum message size results in an exception.</span></span> |
| `MaxReceiveMessageSize` | <span data-ttu-id="521f0-112">4MB</span><span class="sxs-lookup"><span data-stu-id="521f0-112">4 MB</span></span> | <span data-ttu-id="521f0-113">서버에서 받을 수 있는 최대 메시지 크기 (바이트)입니다.</span><span class="sxs-lookup"><span data-stu-id="521f0-113">The maximum message size in bytes that can be received by the server.</span></span> <span data-ttu-id="521f0-114">서버에서이 한도를 초과 하는 메시지를 수신 하는 경우 예외가 throw 됩니다.</span><span class="sxs-lookup"><span data-stu-id="521f0-114">If the server receives a message that exceeds this limit, it throws an exception.</span></span> <span data-ttu-id="521f0-115">이 값을 늘리면 서버가 더 큰 메시지를 수신할 수 있지만 메모리 소비에 부정적인 영향을 줄 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="521f0-115">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="521f0-116">인 `true`경우 서비스 메서드에서 예외가 throw 되 면 자세한 예외 메시지가 클라이언트에 반환 됩니다.</span><span class="sxs-lookup"><span data-stu-id="521f0-116">If `true`, detailed exception messages are returned to clients when an exception is thrown in a service method.</span></span> <span data-ttu-id="521f0-117">기본값은 `false`입니다.</span><span class="sxs-lookup"><span data-stu-id="521f0-117">The default is `false`.</span></span> <span data-ttu-id="521f0-118">`EnableDetailedErrors` 로`true` 설정 하면 중요 한 정보가 누출 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="521f0-118">Setting `EnableDetailedErrors` to `true` can leak sensitive information.</span></span> |
| `CompressionProviders` | <span data-ttu-id="521f0-119">Gzip</span><span class="sxs-lookup"><span data-stu-id="521f0-119">gzip</span></span> | <span data-ttu-id="521f0-120">메시지를 압축 하 고 압축을 푸는 데 사용 되는 압축 공급자의 컬렉션입니다.</span><span class="sxs-lookup"><span data-stu-id="521f0-120">A collection of compression providers used to compress and decompress messages.</span></span> <span data-ttu-id="521f0-121">사용자 지정 압축 공급자를 만들어 컬렉션에 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="521f0-121">Custom compression providers can be created and added to the collection.</span></span> <span data-ttu-id="521f0-122">구성 된 기본 공급자는 **gzip** 압축을 지원 합니다.</span><span class="sxs-lookup"><span data-stu-id="521f0-122">The default configured providers support **gzip** compression.</span></span> |
| `ResponseCompressionAlgorithm` | `null` | <span data-ttu-id="521f0-123">서버에서 보낸 메시지를 압축 하는 데 사용 되는 압축 알고리즘입니다.</span><span class="sxs-lookup"><span data-stu-id="521f0-123">The compression algorithm used to compress messages sent from the server.</span></span> <span data-ttu-id="521f0-124">알고리즘은의 `CompressionProviders`압축 공급자와 일치 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="521f0-124">The algorithm must match a compression provider in `CompressionProviders`.</span></span> <span data-ttu-id="521f0-125">응답을 압축 하는 알고리즘의 경우 클라이언트는 **grpc-수락-인코딩** 헤더에 알고리즘을 전송 하 여 알고리즘을 지원 하도록 지정 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="521f0-125">For the algorithm to compress a response, the client must indicate it supports the algorithm by sending it in the **grpc-accept-encoding** header.</span></span> |
| `ResponseCompressionLevel` | `null` | <span data-ttu-id="521f0-126">서버에서 보낸 메시지를 압축 하는 데 사용 되는 압축 수준입니다.</span><span class="sxs-lookup"><span data-stu-id="521f0-126">The compress level used to compress messages sent from the server.</span></span> |

<span data-ttu-id="521f0-127">`AddGrpc` 에서`Startup.ConfigureServices`호출에 대 한 옵션 대리자를 제공 하 여 모든 서비스에 대해 옵션을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="521f0-127">Options can be configured for all services by providing an options delegate to the `AddGrpc` call in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](~/grpc/configuration/sample/GrcpService/Startup.cs?name=snippet)]

<span data-ttu-id="521f0-128">단일 서비스에 대 한 옵션은에서 `AddGrpc` 제공 하는 전역 옵션을 재정의 하 고 다음을 사용 하 여 `AddServiceOptions<TService>`구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="521f0-128">Options for a single service override the global options provided in `AddGrpc` and can be configured using `AddServiceOptions<TService>`:</span></span>

[!code-csharp[](~/grpc/configuration/sample/GrcpService/Startup2.cs?name=snippet)]

## <a name="configure-client-options"></a><span data-ttu-id="521f0-129">클라이언트 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="521f0-129">Configure client options</span></span>

<span data-ttu-id="521f0-130">gRPC 클라이언트 구성이에 `GrpcChannelOptions`설정 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="521f0-130">gRPC client configuration is set on `GrpcChannelOptions`.</span></span> <span data-ttu-id="521f0-131">다음 표에서는 gRPC 채널을 구성 하는 옵션을 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="521f0-131">The following table describes options for configuring gRPC channels:</span></span>

| <span data-ttu-id="521f0-132">옵션</span><span class="sxs-lookup"><span data-stu-id="521f0-132">Option</span></span> | <span data-ttu-id="521f0-133">Default Value</span><span class="sxs-lookup"><span data-stu-id="521f0-133">Default Value</span></span> | <span data-ttu-id="521f0-134">설명</span><span class="sxs-lookup"><span data-stu-id="521f0-134">Description</span></span> |
| ------ | ------------- | ----------- |
| `HttpClient` | <span data-ttu-id="521f0-135">새 인스턴스</span><span class="sxs-lookup"><span data-stu-id="521f0-135">New instance</span></span> | <span data-ttu-id="521f0-136">Grpc 호출을 수행 하는 데 사용 되는 `HttpClient` 입니다.</span><span class="sxs-lookup"><span data-stu-id="521f0-136">The `HttpClient` used to make gRPC calls.</span></span> <span data-ttu-id="521f0-137">클라이언트를 설정 하 여 사용자 지정 `HttpClientHandler`을 구성 하거나 grpc 호출에 대 한 HTTP 파이프라인에 추가 처리기를 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="521f0-137">A client can be set to configure a custom `HttpClientHandler`, or add additional handlers to the HTTP pipeline for gRPC calls.</span></span> <span data-ttu-id="521f0-138">을 지정 `HttpClient` 하지 않으면 채널에 대 한 `HttpClient` 새 인스턴스가 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="521f0-138">If no `HttpClient` is specified, then a new `HttpClient` instance is created for the channel.</span></span> <span data-ttu-id="521f0-139">자동으로 삭제 됩니다.</span><span class="sxs-lookup"><span data-stu-id="521f0-139">It will automatically be disposed.</span></span> |
| `DisposeHttpClient` | `false` | <span data-ttu-id="521f0-140">및가 지정 `true` `GrpcChannel` 된 경우이 삭제 되 면 인스턴스가삭제됩니다.`HttpClient` `HttpClient`</span><span class="sxs-lookup"><span data-stu-id="521f0-140">If `true`, and an `HttpClient` is specified, then the `HttpClient` instance will be disposed when the `GrpcChannel` is disposed.</span></span> |
| `LoggerFactory` | `null` | <span data-ttu-id="521f0-141">클라이언트에서 grpc 호출에 대 한 정보를 기록 하 는데사용하는입니다.`LoggerFactory`</span><span class="sxs-lookup"><span data-stu-id="521f0-141">The `LoggerFactory` used by the client to log information about gRPC calls.</span></span> <span data-ttu-id="521f0-142">인스턴스 `LoggerFactory` 는 종속성 주입에서 확인 하거나를 사용 하 여 `LoggerFactory.Create`만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="521f0-142">A `LoggerFactory` instance can be resolved from dependency injection or created using `LoggerFactory.Create`.</span></span> <span data-ttu-id="521f0-143">로깅 구성 예제는를 참조 <xref:grpc/diagnostics#grpc-client-logging>하십시오.</span><span class="sxs-lookup"><span data-stu-id="521f0-143">For examples of configuring logging, see <xref:grpc/diagnostics#grpc-client-logging>.</span></span> |
| `MaxSendMessageSize` | `null` | <span data-ttu-id="521f0-144">클라이언트에서 보낼 수 있는 최대 메시지 크기 (바이트)입니다.</span><span class="sxs-lookup"><span data-stu-id="521f0-144">The maximum message size in bytes that can be sent from the client.</span></span> <span data-ttu-id="521f0-145">구성 된 최대 메시지 크기를 초과 하는 메시지를 전송 하려고 하면 예외가 발생 합니다.</span><span class="sxs-lookup"><span data-stu-id="521f0-145">Attempting to send a message that exceeds the configured maximum message size results in an exception.</span></span> |
| `MaxReceiveMessageSize` | <span data-ttu-id="521f0-146">4MB</span><span class="sxs-lookup"><span data-stu-id="521f0-146">4 MB</span></span> | <span data-ttu-id="521f0-147">클라이언트에서 받을 수 있는 최대 메시지 크기 (바이트)입니다.</span><span class="sxs-lookup"><span data-stu-id="521f0-147">The maximum message size in bytes that can be received by the client.</span></span> <span data-ttu-id="521f0-148">클라이언트에서이 한도를 초과 하는 메시지를 수신 하면 예외가 throw 됩니다.</span><span class="sxs-lookup"><span data-stu-id="521f0-148">If the client receives a message that exceeds this limit, it throws an exception.</span></span> <span data-ttu-id="521f0-149">이 값을 늘리면 클라이언트는 더 큰 메시지를 받을 수 있지만 메모리 사용에 부정적인 영향을 줄 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="521f0-149">Increasing this value allows the client to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `Credentials` | `null` | <span data-ttu-id="521f0-150">`ChannelCredentials` 인스턴스입니다.</span><span class="sxs-lookup"><span data-stu-id="521f0-150">A `ChannelCredentials` instance.</span></span> <span data-ttu-id="521f0-151">자격 증명은 gRPC 호출에 인증 메타 데이터를 추가 하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="521f0-151">Credentials are used to add authentication metadata to gRPC calls.</span></span> |
| `CompressionProviders` | <span data-ttu-id="521f0-152">Gzip</span><span class="sxs-lookup"><span data-stu-id="521f0-152">gzip</span></span> | <span data-ttu-id="521f0-153">메시지를 압축 하 고 압축을 푸는 데 사용 되는 압축 공급자의 컬렉션입니다.</span><span class="sxs-lookup"><span data-stu-id="521f0-153">A collection of compression providers used to compress and decompress messages.</span></span> <span data-ttu-id="521f0-154">사용자 지정 압축 공급자를 만들어 컬렉션에 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="521f0-154">Custom compression providers can be created and added to the collection.</span></span> <span data-ttu-id="521f0-155">구성 된 기본 공급자는 **gzip** 압축을 지원 합니다.</span><span class="sxs-lookup"><span data-stu-id="521f0-155">The default configured providers support **gzip** compression.</span></span> |

<span data-ttu-id="521f0-156">다음 예를 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="521f0-156">The following code:</span></span>

* <span data-ttu-id="521f0-157">채널의 최대 송신 및 수신 메시지 크기를 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="521f0-157">Sets the maximum send and receive message size on the channel.</span></span>
* <span data-ttu-id="521f0-158">클라이언트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="521f0-158">Creates a client.</span></span>

[!code-csharp[](~/grpc/configuration/sample/Program.cs?name=snippet&highlight=3-8)]

[!INCLUDE[](~/includes/gRPCazure.md)]

## <a name="additional-resources"></a><span data-ttu-id="521f0-159">추가 자료</span><span class="sxs-lookup"><span data-stu-id="521f0-159">Additional resources</span></span>

* <xref:grpc/aspnetcore>
* <xref:grpc/client>
* <xref:grpc/diagnostics>
* <xref:tutorials/grpc/grpc-start>
