---
title: ASP.NET Core 구성에 대 한 gRPC
author: jamesnk
description: GRPC ASP.NET Core 앱을 구성 하는 방법에 알아봅니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 04/09/2019
uid: grpc/configuration
ms.openlocfilehash: 66dfb9ec136616f10c1b7aaad766e18813b87de4
ms.sourcegitcommit: dd9c73db7853d87b566eef136d2162f648a43b85
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65087335"
---
# <a name="grpc-for-aspnet-core-configuration"></a><span data-ttu-id="64452-103">ASP.NET Core 구성에 대 한 gRPC</span><span class="sxs-lookup"><span data-stu-id="64452-103">gRPC for ASP.NET Core configuration</span></span>

## <a name="configure-services-options"></a><span data-ttu-id="64452-104">서비스 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="64452-104">Configure services options</span></span>

<span data-ttu-id="64452-105">다음 표에서 gRPC 서비스 구성에 대 한 옵션을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="64452-105">The following table describes options for configuring gRPC services:</span></span>

| <span data-ttu-id="64452-106">옵션</span><span class="sxs-lookup"><span data-stu-id="64452-106">Option</span></span> | <span data-ttu-id="64452-107">기본값</span><span class="sxs-lookup"><span data-stu-id="64452-107">Default Value</span></span> | <span data-ttu-id="64452-108">설명</span><span class="sxs-lookup"><span data-stu-id="64452-108">Description</span></span> |
| ------ | ------------- | ----------- |
| `SendMaxMessageSize` | `null` | <span data-ttu-id="64452-109">서버에서 전송 될 수 있는 바이트의 최대 메시지 크기입니다.</span><span class="sxs-lookup"><span data-stu-id="64452-109">The maximum message size in bytes that can be sent from the server.</span></span> <span data-ttu-id="64452-110">예외에서 구성 된 최대 메시지 크기 결과 초과 하는 메시지를 보내려고 시도 합니다.</span><span class="sxs-lookup"><span data-stu-id="64452-110">Attempting to send a message that exceeds the configured maximum message size results in an exception.</span></span> |
| `ReceiveMaxMessageSize` | <span data-ttu-id="64452-111">4MB</span><span class="sxs-lookup"><span data-stu-id="64452-111">4 MB</span></span> | <span data-ttu-id="64452-112">서버에서 받을 수 있는 바이트의 최대 메시지 크기입니다.</span><span class="sxs-lookup"><span data-stu-id="64452-112">The maximum message size in bytes that can be received by the server.</span></span> <span data-ttu-id="64452-113">서버에서이 제한을 초과 하는 메시지를 수신 하는 경우 예외가 throw 됩니다.</span><span class="sxs-lookup"><span data-stu-id="64452-113">If the server receives a message that exceeds this limit, it throws an exception.</span></span> <span data-ttu-id="64452-114">이 값을 늘리면 서버가 더 큰 메시지를 수신할 수 있지만 메모리 소비에 부정적인 영향을 줄 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="64452-114">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="64452-115">경우 `true`자세한 서비스 메서드에서 예외가 throw 될 때 예외 메시지가 클라이언트로 반환 됩니다.</span><span class="sxs-lookup"><span data-stu-id="64452-115">If `true`, detailed exception messages are returned to clients when an exception is thrown in a service method.</span></span> <span data-ttu-id="64452-116">기본값은 `false`입니다.</span><span class="sxs-lookup"><span data-stu-id="64452-116">The default is `false`.</span></span> <span data-ttu-id="64452-117">이 값을 설정 `true` 중요 한 정보를 누출 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="64452-117">Setting this to `true` can leak sensitive information.</span></span> |
| `CompressionProviders` | <span data-ttu-id="64452-118">Gzip</span><span class="sxs-lookup"><span data-stu-id="64452-118">gzip</span></span> | <span data-ttu-id="64452-119">압축 메시지를 압축 하는 데 사용 되는 압축 공급자의 컬렉션입니다.</span><span class="sxs-lookup"><span data-stu-id="64452-119">A collection of compression providers used to compress and decompress messages.</span></span> <span data-ttu-id="64452-120">사용자 지정 압축 공급자 생성 되어 컬렉션에 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="64452-120">Custom compression providers can be created and added to the collection.</span></span> <span data-ttu-id="64452-121">기본 구성 공급자에서 지 원하는 **gzip** 압축 합니다.</span><span class="sxs-lookup"><span data-stu-id="64452-121">The default configured provider supports **gzip** compression.</span></span> |
| `ResponseCompressionAlgorithm` | `null` | <span data-ttu-id="64452-122">서버에서 전송 된 메시지를 압축 하는 데 압축 알고리즘입니다.</span><span class="sxs-lookup"><span data-stu-id="64452-122">The compression algorithm used to compress messages sent from the server.</span></span> <span data-ttu-id="64452-123">알고리즘에서 압축 공급자와 일치 해야 합니다 `CompressionProviders`합니다.</span><span class="sxs-lookup"><span data-stu-id="64452-123">The algorithm must match a compression provider in `CompressionProviders`.</span></span> <span data-ttu-id="64452-124">응답을 압축 하려면 algorthm에 대 한 클라이언트 전송 하 여 알고리즘을 지 원하는 지정 해야 합니다 **grpc-허용 인코딩** 헤더입니다.</span><span class="sxs-lookup"><span data-stu-id="64452-124">For the algorthm to compress a response the client must indicate it supports the algorithm by sending it in the **grpc-accept-encoding** header.</span></span> |
| `ResponseCompressionLevel` | `null` | <span data-ttu-id="64452-125">서버에서 전송 된 메시지를 압축 하는 데 사용 된 압축 수준입니다.</span><span class="sxs-lookup"><span data-stu-id="64452-125">The compress level used to compress messages sent from the server.</span></span> |

<span data-ttu-id="64452-126">하는 옵션 대리자를 제공 하 여 모든 서비스에 대 한 옵션을 구성할 수 있습니다 합니다 `AddGrpc` 에서 호출 `Startup.ConfigureServices`합니다.</span><span class="sxs-lookup"><span data-stu-id="64452-126">Options can be configured for all services by providing an options delegate to the `AddGrpc` call in `Startup.ConfigureServices`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddGrpc(options =>
    {
        options.EnableDetailedErrors = true;
    });
}
```

<span data-ttu-id="64452-127">단일 서비스에 대 한 옵션에서 제공 하는 전역 옵션을 재정의 `AddGrpc` 를 사용 하 여 구성할 수 있습니다 및 `AddServiceOptions<TService>`:</span><span class="sxs-lookup"><span data-stu-id="64452-127">Options for a single service override the global options provided in `AddGrpc` and can be configured using `AddServiceOptions<TService>`:</span></span>

```csharp
services.AddGrpc().AddServiceOptions<MyService>(options =>
{
    options.ReceiveMaxMessageSize = 10 * 1024 * 1024; // 10 megabytes
});
```

## <a name="configure-kestrel-options"></a><span data-ttu-id="64452-128">Kestrel 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="64452-128">Configure Kestrel options</span></span>

<span data-ttu-id="64452-129">Kestrel 서버에 ASP.NET 용 gRPC의 동작에 영향을 주는 구성 옵션이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="64452-129">Kestrel server has configuration options that affect the behavior of gRPC for ASP.NET.</span></span>

### <a name="request-body-data-rate-limit"></a><span data-ttu-id="64452-130">요청 본문 데이터 속도 제한</span><span class="sxs-lookup"><span data-stu-id="64452-130">Request body data rate limit</span></span>

<span data-ttu-id="64452-131">기본적으로 Kestrel 서버 적용을 [최소 요청 본문 데이터 속도](
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>)합니다.</span><span class="sxs-lookup"><span data-stu-id="64452-131">By default, the Kestrel server imposes a [minimum request body data rate](
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>).</span></span> <span data-ttu-id="64452-132">스트리밍 호출이 이중와 스트리밍 클라이언트에 대 한이 속도 충족할 수 있습니다 하 고 연결 시간이 초과 될 수 있습니다. 최소 요청 본문 데이터 속도 제한 gRPC 서비스 스트리밍 클라이언트 및 이중 교환 패턴 스트리밍 호출이 포함 된 경우 비활성화 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="64452-132">For client streaming and duplex streaming calls, this rate may not be satisfied and the connection may be timed out. The minimum request body data rate limit must be disabled when the gRPC service includes client streaming and duplex streaming calls:</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
         Host.CreateDefaultBuilder(args)
    .ConfigureWebHostDefaults(webBuilder =>
    {
        webBuilder.UseStartup<Startup>();
        webBuilder.ConfigureKestrel((context, options) =>
        {
            options.Limits.MinRequestBodyDataRate = null;
        });
    });
}
```

## <a name="additional-resources"></a><span data-ttu-id="64452-133">추가 자료</span><span class="sxs-lookup"><span data-stu-id="64452-133">Additional resources</span></span>

* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/migration>
