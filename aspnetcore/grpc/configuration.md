---
title: ASP.NET Core 구성에 대 한 gRPC
author: jamesnk
description: GRPC ASP.NET Core 앱을 구성 하는 방법에 알아봅니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 04/09/2019
uid: grpc/configuration
ms.openlocfilehash: 851c9ca1f7d62f6f368df66bb38eb4bbaf64bf32
ms.sourcegitcommit: 5d384db2fa9373a93b5d15e985fb34430e49ad7a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66041890"
---
# <a name="grpc-for-aspnet-core-configuration"></a><span data-ttu-id="70d23-103">ASP.NET Core 구성에 대 한 gRPC</span><span class="sxs-lookup"><span data-stu-id="70d23-103">gRPC for ASP.NET Core configuration</span></span>

## <a name="configure-services-options"></a><span data-ttu-id="70d23-104">서비스 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="70d23-104">Configure services options</span></span>

<span data-ttu-id="70d23-105">다음 표에서 gRPC 서비스 구성에 대 한 옵션을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="70d23-105">The following table describes options for configuring gRPC services:</span></span>

| <span data-ttu-id="70d23-106">옵션</span><span class="sxs-lookup"><span data-stu-id="70d23-106">Option</span></span> | <span data-ttu-id="70d23-107">기본값</span><span class="sxs-lookup"><span data-stu-id="70d23-107">Default Value</span></span> | <span data-ttu-id="70d23-108">설명</span><span class="sxs-lookup"><span data-stu-id="70d23-108">Description</span></span> |
| ------ | ------------- | ----------- |
| `SendMaxMessageSize` | `null` | <span data-ttu-id="70d23-109">서버에서 전송 될 수 있는 바이트의 최대 메시지 크기입니다.</span><span class="sxs-lookup"><span data-stu-id="70d23-109">The maximum message size in bytes that can be sent from the server.</span></span> <span data-ttu-id="70d23-110">예외에서 구성 된 최대 메시지 크기 결과 초과 하는 메시지를 보내려고 시도 합니다.</span><span class="sxs-lookup"><span data-stu-id="70d23-110">Attempting to send a message that exceeds the configured maximum message size results in an exception.</span></span> |
| `ReceiveMaxMessageSize` | <span data-ttu-id="70d23-111">4MB</span><span class="sxs-lookup"><span data-stu-id="70d23-111">4 MB</span></span> | <span data-ttu-id="70d23-112">서버에서 받을 수 있는 바이트의 최대 메시지 크기입니다.</span><span class="sxs-lookup"><span data-stu-id="70d23-112">The maximum message size in bytes that can be received by the server.</span></span> <span data-ttu-id="70d23-113">서버에서이 제한을 초과 하는 메시지를 수신 하는 경우 예외가 throw 됩니다.</span><span class="sxs-lookup"><span data-stu-id="70d23-113">If the server receives a message that exceeds this limit, it throws an exception.</span></span> <span data-ttu-id="70d23-114">이 값을 늘리면 서버가 더 큰 메시지를 수신할 수 있지만 메모리 소비에 부정적인 영향을 줄 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="70d23-114">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="70d23-115">경우 `true`자세한 서비스 메서드에서 예외가 throw 될 때 예외 메시지가 클라이언트로 반환 됩니다.</span><span class="sxs-lookup"><span data-stu-id="70d23-115">If `true`, detailed exception messages are returned to clients when an exception is thrown in a service method.</span></span> <span data-ttu-id="70d23-116">기본값은 `false`입니다.</span><span class="sxs-lookup"><span data-stu-id="70d23-116">The default is `false`.</span></span> <span data-ttu-id="70d23-117">이 값을 설정 `true` 중요 한 정보를 누출 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="70d23-117">Setting this to `true` can leak sensitive information.</span></span> |
| `CompressionProviders` | <span data-ttu-id="70d23-118">Gzip</span><span class="sxs-lookup"><span data-stu-id="70d23-118">gzip</span></span> | <span data-ttu-id="70d23-119">압축 메시지를 압축 하는 데 사용 되는 압축 공급자의 컬렉션입니다.</span><span class="sxs-lookup"><span data-stu-id="70d23-119">A collection of compression providers used to compress and decompress messages.</span></span> <span data-ttu-id="70d23-120">사용자 지정 압축 공급자 생성 되어 컬렉션에 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="70d23-120">Custom compression providers can be created and added to the collection.</span></span> <span data-ttu-id="70d23-121">기본 구성 공급자에서 지 원하는 **gzip** 압축 합니다.</span><span class="sxs-lookup"><span data-stu-id="70d23-121">The default configured provider supports **gzip** compression.</span></span> |
| `ResponseCompressionAlgorithm` | `null` | <span data-ttu-id="70d23-122">서버에서 전송 된 메시지를 압축 하는 데 압축 알고리즘입니다.</span><span class="sxs-lookup"><span data-stu-id="70d23-122">The compression algorithm used to compress messages sent from the server.</span></span> <span data-ttu-id="70d23-123">알고리즘에서 압축 공급자와 일치 해야 합니다 `CompressionProviders`합니다.</span><span class="sxs-lookup"><span data-stu-id="70d23-123">The algorithm must match a compression provider in `CompressionProviders`.</span></span> <span data-ttu-id="70d23-124">응답을 압축 하려면 algorthm에 대 한 클라이언트 전송 하 여 알고리즘을 지 원하는 지정 해야 합니다 **grpc-허용 인코딩** 헤더입니다.</span><span class="sxs-lookup"><span data-stu-id="70d23-124">For the algorthm to compress a response the client must indicate it supports the algorithm by sending it in the **grpc-accept-encoding** header.</span></span> |
| `ResponseCompressionLevel` | `null` | <span data-ttu-id="70d23-125">서버에서 전송 된 메시지를 압축 하는 데 사용 된 압축 수준입니다.</span><span class="sxs-lookup"><span data-stu-id="70d23-125">The compress level used to compress messages sent from the server.</span></span> |

<span data-ttu-id="70d23-126">하는 옵션 대리자를 제공 하 여 모든 서비스에 대 한 옵션을 구성할 수 있습니다 합니다 `AddGrpc` 에서 호출 `Startup.ConfigureServices`합니다.</span><span class="sxs-lookup"><span data-stu-id="70d23-126">Options can be configured for all services by providing an options delegate to the `AddGrpc` call in `Startup.ConfigureServices`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddGrpc(options =>
    {
        options.EnableDetailedErrors = true;
    });
}
```

<span data-ttu-id="70d23-127">단일 서비스에 대 한 옵션에서 제공 하는 전역 옵션을 재정의 `AddGrpc` 를 사용 하 여 구성할 수 있습니다 및 `AddServiceOptions<TService>`:</span><span class="sxs-lookup"><span data-stu-id="70d23-127">Options for a single service override the global options provided in `AddGrpc` and can be configured using `AddServiceOptions<TService>`:</span></span>

```csharp
services.AddGrpc().AddServiceOptions<MyService>(options =>
{
    options.ReceiveMaxMessageSize = 10 * 1024 * 1024; // 10 megabytes
});
```

## <a name="additional-resources"></a><span data-ttu-id="70d23-128">추가 자료</span><span class="sxs-lookup"><span data-stu-id="70d23-128">Additional resources</span></span>

* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/migration>
