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
# <a name="grpc-for-aspnet-core-configuration"></a><span data-ttu-id="bc3a6-103">ASP.NET Core 구성을 위한 gRPC</span><span class="sxs-lookup"><span data-stu-id="bc3a6-103">gRPC for ASP.NET Core configuration</span></span>

## <a name="configure-services-options"></a><span data-ttu-id="bc3a6-104">서비스 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="bc3a6-104">Configure services options</span></span>

<span data-ttu-id="bc3a6-105">다음 표에서는 gRPC 서비스를 구성 하는 옵션을 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="bc3a6-105">The following table describes options for configuring gRPC services:</span></span>

| <span data-ttu-id="bc3a6-106">옵션</span><span class="sxs-lookup"><span data-stu-id="bc3a6-106">Option</span></span> | <span data-ttu-id="bc3a6-107">기본값</span><span class="sxs-lookup"><span data-stu-id="bc3a6-107">Default Value</span></span> | <span data-ttu-id="bc3a6-108">설명</span><span class="sxs-lookup"><span data-stu-id="bc3a6-108">Description</span></span> |
| ------ | ------------- | ----------- |
| `MaxSendMessageSize` | `null` | <span data-ttu-id="bc3a6-109">서버에서 보낼 수 있는 최대 메시지 크기 (바이트)입니다.</span><span class="sxs-lookup"><span data-stu-id="bc3a6-109">The maximum message size in bytes that can be sent from the server.</span></span> <span data-ttu-id="bc3a6-110">구성 된 최대 메시지 크기를 초과 하는 메시지를 전송 하려고 하면 예외가 발생 합니다.</span><span class="sxs-lookup"><span data-stu-id="bc3a6-110">Attempting to send a message that exceeds the configured maximum message size results in an exception.</span></span> |
| `MaxReceiveMessageSize` | <span data-ttu-id="bc3a6-111">4MB</span><span class="sxs-lookup"><span data-stu-id="bc3a6-111">4 MB</span></span> | <span data-ttu-id="bc3a6-112">서버에서 받을 수 있는 최대 메시지 크기 (바이트)입니다.</span><span class="sxs-lookup"><span data-stu-id="bc3a6-112">The maximum message size in bytes that can be received by the server.</span></span> <span data-ttu-id="bc3a6-113">서버에서이 한도를 초과 하는 메시지를 수신 하는 경우 예외가 throw 됩니다.</span><span class="sxs-lookup"><span data-stu-id="bc3a6-113">If the server receives a message that exceeds this limit, it throws an exception.</span></span> <span data-ttu-id="bc3a6-114">이 값을 늘리면 서버가 더 큰 메시지를 수신할 수 있지만 메모리 소비에 부정적인 영향을 줄 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="bc3a6-114">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="bc3a6-115">인 `true`경우 서비스 메서드에서 예외가 throw 되 면 자세한 예외 메시지가 클라이언트에 반환 됩니다.</span><span class="sxs-lookup"><span data-stu-id="bc3a6-115">If `true`, detailed exception messages are returned to clients when an exception is thrown in a service method.</span></span> <span data-ttu-id="bc3a6-116">기본값은 `false`입니다.</span><span class="sxs-lookup"><span data-stu-id="bc3a6-116">The default is `false`.</span></span> <span data-ttu-id="bc3a6-117">`EnableDetailedErrors` 로`true` 설정 하면 중요 한 정보가 누출 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="bc3a6-117">Setting `EnableDetailedErrors` to `true` can leak sensitive information.</span></span> |
| `CompressionProviders` | <span data-ttu-id="bc3a6-118">gzip, deflate</span><span class="sxs-lookup"><span data-stu-id="bc3a6-118">gzip, deflate</span></span> | <span data-ttu-id="bc3a6-119">메시지를 압축 하 고 압축을 푸는 데 사용 되는 압축 공급자의 컬렉션입니다.</span><span class="sxs-lookup"><span data-stu-id="bc3a6-119">A collection of compression providers used to compress and decompress messages.</span></span> <span data-ttu-id="bc3a6-120">사용자 지정 압축 공급자를 만들어 컬렉션에 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="bc3a6-120">Custom compression providers can be created and added to the collection.</span></span> <span data-ttu-id="bc3a6-121">구성 된 기본 공급자는 **gzip** 및 **deflate** 압축을 지원 합니다.</span><span class="sxs-lookup"><span data-stu-id="bc3a6-121">The default configured providers support **gzip** and **deflate** compression.</span></span> |
| `ResponseCompressionAlgorithm` | `null` | <span data-ttu-id="bc3a6-122">서버에서 보낸 메시지를 압축 하는 데 사용 되는 압축 알고리즘입니다.</span><span class="sxs-lookup"><span data-stu-id="bc3a6-122">The compression algorithm used to compress messages sent from the server.</span></span> <span data-ttu-id="bc3a6-123">알고리즘은의 `CompressionProviders`압축 공급자와 일치 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="bc3a6-123">The algorithm must match a compression provider in `CompressionProviders`.</span></span> <span data-ttu-id="bc3a6-124">응답을 압축 하는 알고리즘의 경우 클라이언트는 **grpc-수락-인코딩** 헤더에 알고리즘을 전송 하 여 알고리즘을 지원 하도록 지정 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="bc3a6-124">For the algorithm to compress a response, the client must indicate it supports the algorithm by sending it in the **grpc-accept-encoding** header.</span></span> |
| `ResponseCompressionLevel` | `null` | <span data-ttu-id="bc3a6-125">서버에서 보낸 메시지를 압축 하는 데 사용 되는 압축 수준입니다.</span><span class="sxs-lookup"><span data-stu-id="bc3a6-125">The compress level used to compress messages sent from the server.</span></span> |

<span data-ttu-id="bc3a6-126">`AddGrpc` 에서`Startup.ConfigureServices`호출에 대 한 옵션 대리자를 제공 하 여 모든 서비스에 대해 옵션을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="bc3a6-126">Options can be configured for all services by providing an options delegate to the `AddGrpc` call in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](~/grpc/configuration/sample/GrcpService/Startup.cs?name=snippet)]

<span data-ttu-id="bc3a6-127">단일 서비스에 대 한 옵션은에서 `AddGrpc` 제공 하는 전역 옵션을 재정의 하 고 다음을 사용 하 여 `AddServiceOptions<TService>`구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="bc3a6-127">Options for a single service override the global options provided in `AddGrpc` and can be configured using `AddServiceOptions<TService>`:</span></span>

[!code-csharp[](~/grpc/configuration/sample/GrcpService/Startup2.cs?name=snippet)]

## <a name="configure-client-options"></a><span data-ttu-id="bc3a6-128">클라이언트 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="bc3a6-128">Configure client options</span></span>

<span data-ttu-id="bc3a6-129">다음 표에서는 gRPC 채널을 구성 하는 옵션을 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="bc3a6-129">The following table describes options for configuring gRPC channels:</span></span>

| <span data-ttu-id="bc3a6-130">옵션</span><span class="sxs-lookup"><span data-stu-id="bc3a6-130">Option</span></span> | <span data-ttu-id="bc3a6-131">기본값</span><span class="sxs-lookup"><span data-stu-id="bc3a6-131">Default Value</span></span> | <span data-ttu-id="bc3a6-132">설명</span><span class="sxs-lookup"><span data-stu-id="bc3a6-132">Description</span></span> |
| ------ | ------------- | ----------- |
| `HttpClient` | <span data-ttu-id="bc3a6-133">새 인스턴스</span><span class="sxs-lookup"><span data-stu-id="bc3a6-133">New instance</span></span> | <span data-ttu-id="bc3a6-134">Grpc 호출을 수행 하는 데 사용 되는 `HttpClient` 입니다.</span><span class="sxs-lookup"><span data-stu-id="bc3a6-134">The `HttpClient` used to make gRPC calls.</span></span> <span data-ttu-id="bc3a6-135">클라이언트를 설정 하 여 사용자 지정 `HttpClientHandler`을 구성 하거나 grpc 호출에 대 한 HTTP 파이프라인에 추가 처리기를 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="bc3a6-135">A client can be set to configure a custom `HttpClientHandler`, or add additional handlers to the HTTP pipeline for gRPC calls.</span></span> <span data-ttu-id="bc3a6-136">기본적으로 새 `HttpClient` 인스턴스가 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="bc3a6-136">By default a new `HttpClient` instance is created.</span></span> |
| `MaxSendMessageSize` | `null` | <span data-ttu-id="bc3a6-137">클라이언트에서 보낼 수 있는 최대 메시지 크기 (바이트)입니다.</span><span class="sxs-lookup"><span data-stu-id="bc3a6-137">The maximum message size in bytes that can be sent from the client.</span></span> <span data-ttu-id="bc3a6-138">구성 된 최대 메시지 크기를 초과 하는 메시지를 전송 하려고 하면 예외가 발생 합니다.</span><span class="sxs-lookup"><span data-stu-id="bc3a6-138">Attempting to send a message that exceeds the configured maximum message size results in an exception.</span></span> |
| `MaxReceiveMessageSize` | <span data-ttu-id="bc3a6-139">4MB</span><span class="sxs-lookup"><span data-stu-id="bc3a6-139">4 MB</span></span> | <span data-ttu-id="bc3a6-140">클라이언트에서 받을 수 있는 최대 메시지 크기 (바이트)입니다.</span><span class="sxs-lookup"><span data-stu-id="bc3a6-140">The maximum message size in bytes that can be received by the client.</span></span> <span data-ttu-id="bc3a6-141">서버에서이 한도를 초과 하는 메시지를 수신 하는 경우 예외가 throw 됩니다.</span><span class="sxs-lookup"><span data-stu-id="bc3a6-141">If the server receives a message that exceeds this limit, it throws an exception.</span></span> <span data-ttu-id="bc3a6-142">이 값을 늘리면 서버가 더 큰 메시지를 수신할 수 있지만 메모리 소비에 부정적인 영향을 줄 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="bc3a6-142">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `TransportOptions` | `null` | <span data-ttu-id="bc3a6-143">전송 옵션 채널에서 gRPC 서비스를 호출 하는 방법을 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="bc3a6-143">Transport options configure how the channel calls the gRPC service.</span></span> <span data-ttu-id="bc3a6-144">현재 유일 하 게 구현 `HttpClientTransport` 되는 옵션을 사용 `HttpClient` 하 여 grpc에서 사용 되는를 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="bc3a6-144">Currently the only implementation is `HttpClientTransport` options lets you specify the `HttpClient` used by gRPC.</span></span> |
| `Credentials` | `null` | <span data-ttu-id="bc3a6-145">`ChannelCredentials` 인스턴스입니다.</span><span class="sxs-lookup"><span data-stu-id="bc3a6-145">A `ChannelCredentials` instance.</span></span> <span data-ttu-id="bc3a6-146">자격 증명은 gRPC 호출에 인증 메타 데이터를 추가 하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="bc3a6-146">Credentials are used to add authentication metadata to gRPC calls.</span></span> |
| `CompressionProviders` | <span data-ttu-id="bc3a6-147">gzip, deflate</span><span class="sxs-lookup"><span data-stu-id="bc3a6-147">gzip, deflate</span></span> | <span data-ttu-id="bc3a6-148">메시지를 압축 하 고 압축을 푸는 데 사용 되는 압축 공급자의 컬렉션입니다.</span><span class="sxs-lookup"><span data-stu-id="bc3a6-148">A collection of compression providers used to compress and decompress messages.</span></span> <span data-ttu-id="bc3a6-149">사용자 지정 압축 공급자를 만들어 컬렉션에 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="bc3a6-149">Custom compression providers can be created and added to the collection.</span></span> <span data-ttu-id="bc3a6-150">구성 된 기본 공급자는 **gzip** 및 **deflate** 압축을 지원 합니다.</span><span class="sxs-lookup"><span data-stu-id="bc3a6-150">The default configured providers support **gzip** and **deflate** compression.</span></span> |

<span data-ttu-id="bc3a6-151">다음 예를 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="bc3a6-151">The following code:</span></span>

* <span data-ttu-id="bc3a6-152">채널의 최대 송신 및 수신 메시지 크기를 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="bc3a6-152">Sets the maximum send and receive message size on the channel.</span></span>
* <span data-ttu-id="bc3a6-153">클라이언트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="bc3a6-153">Creates a client.</span></span>

[!code-csharp[](~/grpc/configuration/sample/Program.cs?name=snippet&highlight=3-8)]

## <a name="additional-resources"></a><span data-ttu-id="bc3a6-154">추가 자료</span><span class="sxs-lookup"><span data-stu-id="bc3a6-154">Additional resources</span></span>

* <xref:grpc/aspnetcore>
* <xref:grpc/client>
* <xref:tutorials/grpc/grpc-start>
