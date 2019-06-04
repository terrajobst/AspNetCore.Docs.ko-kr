---
title: ASP.NET Core 구성에 대 한 gRPC
author: jamesnk
description: GRPC ASP.NET Core 앱을 구성 하는 방법에 알아봅니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 5/30/2019
uid: grpc/configuration
ms.openlocfilehash: 1f8250dc9aa8b82da384ee28287011baa19dc11f
ms.sourcegitcommit: a1364109d11d414121a6337b611bee61d6e489e9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66491237"
---
# <a name="grpc-for-aspnet-core-configuration"></a><span data-ttu-id="4b9eb-103">ASP.NET Core 구성에 대 한 gRPC</span><span class="sxs-lookup"><span data-stu-id="4b9eb-103">gRPC for ASP.NET Core configuration</span></span>

## <a name="configure-services-options"></a><span data-ttu-id="4b9eb-104">서비스 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="4b9eb-104">Configure services options</span></span>

<span data-ttu-id="4b9eb-105">다음 표에서 gRPC 서비스 구성에 대 한 옵션을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="4b9eb-105">The following table describes options for configuring gRPC services:</span></span>

| <span data-ttu-id="4b9eb-106">옵션</span><span class="sxs-lookup"><span data-stu-id="4b9eb-106">Option</span></span> | <span data-ttu-id="4b9eb-107">기본값</span><span class="sxs-lookup"><span data-stu-id="4b9eb-107">Default Value</span></span> | <span data-ttu-id="4b9eb-108">설명</span><span class="sxs-lookup"><span data-stu-id="4b9eb-108">Description</span></span> |
| ------ | ------------- | ----------- |
| `SendMaxMessageSize` | `null` | <span data-ttu-id="4b9eb-109">서버에서 전송 될 수 있는 바이트의 최대 메시지 크기입니다.</span><span class="sxs-lookup"><span data-stu-id="4b9eb-109">The maximum message size in bytes that can be sent from the server.</span></span> <span data-ttu-id="4b9eb-110">예외에서 구성 된 최대 메시지 크기 결과 초과 하는 메시지를 보내려고 시도 합니다.</span><span class="sxs-lookup"><span data-stu-id="4b9eb-110">Attempting to send a message that exceeds the configured maximum message size results in an exception.</span></span> |
| `ReceiveMaxMessageSize` | <span data-ttu-id="4b9eb-111">4MB</span><span class="sxs-lookup"><span data-stu-id="4b9eb-111">4 MB</span></span> | <span data-ttu-id="4b9eb-112">서버에서 받을 수 있는 바이트의 최대 메시지 크기입니다.</span><span class="sxs-lookup"><span data-stu-id="4b9eb-112">The maximum message size in bytes that can be received by the server.</span></span> <span data-ttu-id="4b9eb-113">서버에서이 제한을 초과 하는 메시지를 수신 하는 경우 예외가 throw 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4b9eb-113">If the server receives a message that exceeds this limit, it throws an exception.</span></span> <span data-ttu-id="4b9eb-114">이 값을 늘리면 서버가 더 큰 메시지를 수신할 수 있지만 메모리 소비에 부정적인 영향을 줄 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4b9eb-114">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="4b9eb-115">경우 `true`자세한 서비스 메서드에서 예외가 throw 될 때 예외 메시지가 클라이언트로 반환 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4b9eb-115">If `true`, detailed exception messages are returned to clients when an exception is thrown in a service method.</span></span> <span data-ttu-id="4b9eb-116">기본값은 `false`입니다.</span><span class="sxs-lookup"><span data-stu-id="4b9eb-116">The default is `false`.</span></span> <span data-ttu-id="4b9eb-117">설정 `EnableDetailedErrors` 에 `true` 중요 한 정보를 누출 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4b9eb-117">Setting `EnableDetailedErrors` to `true` can leak sensitive information.</span></span> |
| `CompressionProviders` | <span data-ttu-id="4b9eb-118">Gzip</span><span class="sxs-lookup"><span data-stu-id="4b9eb-118">gzip</span></span> | <span data-ttu-id="4b9eb-119">압축 메시지를 압축 하는 데 사용 되는 압축 공급자의 컬렉션입니다.</span><span class="sxs-lookup"><span data-stu-id="4b9eb-119">A collection of compression providers used to compress and decompress messages.</span></span> <span data-ttu-id="4b9eb-120">사용자 지정 압축 공급자 생성 되어 컬렉션에 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4b9eb-120">Custom compression providers can be created and added to the collection.</span></span> <span data-ttu-id="4b9eb-121">기본 구성 공급자에서 지 원하는 **gzip** 압축 합니다.</span><span class="sxs-lookup"><span data-stu-id="4b9eb-121">The default configured provider supports **gzip** compression.</span></span> |
| `ResponseCompressionAlgorithm` | `null` | <span data-ttu-id="4b9eb-122">서버에서 전송 된 메시지를 압축 하는 데 압축 알고리즘입니다.</span><span class="sxs-lookup"><span data-stu-id="4b9eb-122">The compression algorithm used to compress messages sent from the server.</span></span> <span data-ttu-id="4b9eb-123">알고리즘에서 압축 공급자와 일치 해야 합니다 `CompressionProviders`합니다.</span><span class="sxs-lookup"><span data-stu-id="4b9eb-123">The algorithm must match a compression provider in `CompressionProviders`.</span></span> <span data-ttu-id="4b9eb-124">응답 압축 알고리즘에 대 한 클라이언트 전송 하 여 알고리즘을 지 원하는 지정 해야 합니다 **grpc-허용 인코딩** 헤더입니다.</span><span class="sxs-lookup"><span data-stu-id="4b9eb-124">For the algorithm to compress a response, the client must indicate it supports the algorithm by sending it in the **grpc-accept-encoding** header.</span></span> |
| `ResponseCompressionLevel` | `null` | <span data-ttu-id="4b9eb-125">서버에서 전송 된 메시지를 압축 하는 데 사용 된 압축 수준입니다.</span><span class="sxs-lookup"><span data-stu-id="4b9eb-125">The compress level used to compress messages sent from the server.</span></span> |

<span data-ttu-id="4b9eb-126">하는 옵션 대리자를 제공 하 여 모든 서비스에 대 한 옵션을 구성할 수 있습니다 합니다 `AddGrpc` 에서 호출 `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="4b9eb-126">Options can be configured for all services by providing an options delegate to the `AddGrpc` call in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](~/grpc/configuration/sample/GrcpService/Startup.cs?name=snippet)]

<span data-ttu-id="4b9eb-127">단일 서비스에 대 한 옵션에서 제공 하는 전역 옵션을 재정의 `AddGrpc` 를 사용 하 여 구성할 수 있습니다 및 `AddServiceOptions<TService>`:</span><span class="sxs-lookup"><span data-stu-id="4b9eb-127">Options for a single service override the global options provided in `AddGrpc` and can be configured using `AddServiceOptions<TService>`:</span></span>

[!code-csharp[](~/grpc/configuration/sample/GrcpService/Startup2.cs?name=snippet)]

## <a name="configure-client-options"></a><span data-ttu-id="4b9eb-128">클라이언트 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="4b9eb-128">Configure client options</span></span>

<span data-ttu-id="4b9eb-129">다음 코드를 설정 하는 클라이언트 최대 송신 및 수신 메시지 크기:</span><span class="sxs-lookup"><span data-stu-id="4b9eb-129">The following code sets the client maximum send and receive message size:</span></span>

[!code-csharp[](~/grpc/configuration/sample/Program.cs?name=snippet&highlight=3-6)]

## <a name="additional-resources"></a><span data-ttu-id="4b9eb-130">추가 자료</span><span class="sxs-lookup"><span data-stu-id="4b9eb-130">Additional resources</span></span>

* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/migration>
