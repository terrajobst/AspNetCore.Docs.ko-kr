---
title: ASP.NET Core에 대 한 gRPC의 보안 고려 사항
author: jamesnk
description: ASP.NET Core에 대 한 gRPC의 보안 고려 사항에 대해 알아봅니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 07/07/2019
uid: grpc/security
ms.openlocfilehash: f84bec0ef485b701b2be36384a2e49b9b28e473d
ms.sourcegitcommit: 8b36f75b8931ae3f656e2a8e63572080adc78513
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/05/2019
ms.locfileid: "70310369"
---
# <a name="security-considerations-in-grpc-for-aspnet-core"></a><span data-ttu-id="6a34b-103">ASP.NET Core에 대 한 gRPC의 보안 고려 사항</span><span class="sxs-lookup"><span data-stu-id="6a34b-103">Security considerations in gRPC for ASP.NET Core</span></span>

<span data-ttu-id="6a34b-104">별 [뉴턴-킹](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="6a34b-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="6a34b-105">이 문서에서는 .NET Core로 gRPC를 보호 하는 방법에 대 한 정보를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="6a34b-105">This article provides information on securing gRPC with .NET Core.</span></span>

## <a name="transport-security"></a><span data-ttu-id="6a34b-106">전송 보안</span><span class="sxs-lookup"><span data-stu-id="6a34b-106">Transport security</span></span>

<span data-ttu-id="6a34b-107">gRPC 메시지는 HTTP/2를 사용 하 여 전송 및 수신 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6a34b-107">gRPC messages are sent and received using HTTP/2.</span></span> <span data-ttu-id="6a34b-108">다음을 권장 합니다.</span><span class="sxs-lookup"><span data-stu-id="6a34b-108">We recommend:</span></span>

* <span data-ttu-id="6a34b-109">[TLS (전송 계층 보안)](https://tools.ietf.org/html/rfc5246) 는 프로덕션 grpc 앱에서 메시지를 보호 하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6a34b-109">[Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246) be used to secure messages in production gRPC apps.</span></span>
* <span data-ttu-id="6a34b-110">gRPC 서비스는 보안 포트만 수신 하 고 응답 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="6a34b-110">gRPC services should only listen and respond over secured ports.</span></span>

<span data-ttu-id="6a34b-111">TLS는 Kestrel에서 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6a34b-111">TLS is configured in Kestrel.</span></span> <span data-ttu-id="6a34b-112">Kestrel 끝점을 구성 하는 방법에 대 한 자세한 내용은 [Kestrel 끝점 구성](xref:fundamentals/servers/kestrel#endpoint-configuration)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="6a34b-112">For more information on configuring Kestrel endpoints, see [Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration).</span></span>

## <a name="exceptions"></a><span data-ttu-id="6a34b-113">예외</span><span class="sxs-lookup"><span data-stu-id="6a34b-113">Exceptions</span></span>

<span data-ttu-id="6a34b-114">일반적으로 예외 메시지는 민감한 데이터로 간주되어 클라이언트에게 노출되어서는 안 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6a34b-114">Exception messages are generally considered sensitive data that shouldn't be revealed to a client.</span></span> <span data-ttu-id="6a34b-115">기본적으로 gRPC는 gRPC 서비스에서 throw 되는 예외에 대 한 세부 정보를 클라이언트에 보내지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="6a34b-115">By default, gRPC doesn't send the details of an exception thrown by a gRPC service to the client.</span></span> <span data-ttu-id="6a34b-116">대신 클라이언트는 오류가 발생했음을 나타내는 일반적인 메시지를 수신합니다.</span><span class="sxs-lookup"><span data-stu-id="6a34b-116">Instead, the client receives a generic message indicating an error occurred.</span></span> <span data-ttu-id="6a34b-117">[EnableDetailedErrors](xref:grpc/configuration#configure-services-options)를 사용 하 여 클라이언트에 대 한 예외 메시지 배달 (예: 개발 또는 테스트)을 재정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6a34b-117">Exception message delivery to the client can be overridden (for example, in development or test) with [EnableDetailedErrors](xref:grpc/configuration#configure-services-options).</span></span> <span data-ttu-id="6a34b-118">프로덕션 앱에서 예외 메시지를 클라이언트에 노출 해서는 안 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6a34b-118">Exception messages shouldn't be exposed to the client in production apps.</span></span>

## <a name="message-size-limits"></a><span data-ttu-id="6a34b-119">메시지 크기 제한</span><span class="sxs-lookup"><span data-stu-id="6a34b-119">Message size limits</span></span>

<span data-ttu-id="6a34b-120">GRPC 클라이언트 및 서비스에 대 한 들어오는 메시지는 메모리에 로드 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6a34b-120">Incoming messages to gRPC clients and services are loaded into memory.</span></span> <span data-ttu-id="6a34b-121">메시지 크기 제한은 gRPC가 과도 한 리소스를 사용 하는 것을 방지 하는 메커니즘입니다.</span><span class="sxs-lookup"><span data-stu-id="6a34b-121">Message size limits are a mechanism to help prevent gRPC from consuming excessive resources.</span></span>

<span data-ttu-id="6a34b-122">gRPC는 메시지당 크기 제한을 사용 하 여 들어오고 나가는 메시지를 관리 합니다.</span><span class="sxs-lookup"><span data-stu-id="6a34b-122">gRPC uses per-message size limits to manage incoming and outgoing messages.</span></span> <span data-ttu-id="6a34b-123">기본적으로 gRPC는 들어오는 메시지를 4mb로 제한 합니다.</span><span class="sxs-lookup"><span data-stu-id="6a34b-123">By default, gRPC limits incoming messages to 4 MB.</span></span> <span data-ttu-id="6a34b-124">보내는 메시지에 대 한 제한은 없습니다.</span><span class="sxs-lookup"><span data-stu-id="6a34b-124">There is no limit on outgoing messages.</span></span>

<span data-ttu-id="6a34b-125">서버에서 다음을 사용 하 여 `AddGrpc`앱의 모든 서비스에 대해 grpc 메시지 제한을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6a34b-125">On the server, gRPC message limits can be configured for all services in an app with `AddGrpc`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddGrpc(options =>
    {
        options.MaxReceiveMessageSize = 1 * 1024 * 1024; // 1 MB
        options.MaxSendMessageSize = 1 * 1024 * 1024; // 1 MB
    });
}
```

<span data-ttu-id="6a34b-126">를 사용 하 여 `AddServiceOptions<TService>`개별 서비스에 대해 제한을 구성할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6a34b-126">Limits can also be configured for an individual service using `AddServiceOptions<TService>`.</span></span> <span data-ttu-id="6a34b-127">메시지 크기 제한을 구성 하는 방법에 대 한 자세한 내용은 [Grpc 구성](xref:grpc/configuration)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="6a34b-127">For more information on configuring message size limits, see [gRPC configuration](xref:grpc/configuration).</span></span>

## <a name="client-certificate-validation"></a><span data-ttu-id="6a34b-128">클라이언트 인증서 유효성 검사</span><span class="sxs-lookup"><span data-stu-id="6a34b-128">Client certificate validation</span></span>

<span data-ttu-id="6a34b-129">[클라이언트 인증서](https://tools.ietf.org/html/rfc5246#section-7.4.4) 는 연결이 설정 될 때 초기에 유효성이 검사 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6a34b-129">[Client certificates](https://tools.ietf.org/html/rfc5246#section-7.4.4) are initially validated when the connection is established.</span></span> <span data-ttu-id="6a34b-130">기본적으로 Kestrel은 연결의 클라이언트 인증서에 대 한 추가 유효성 검사를 수행 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="6a34b-130">By default, Kestrel doesn't perform additional validation of a connection's client certificate.</span></span>

<span data-ttu-id="6a34b-131">클라이언트 인증서로 보호 되는 gRPC 서비스를 사용 하는 것이 [AspNetCore](xref:security/authentication/certauth) 패키지를 사용 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="6a34b-131">We recommend that gRPC services secured by client certificates use the [Microsoft.AspNetCore.Authentication.Certificate](xref:security/authentication/certauth) package.</span></span> <span data-ttu-id="6a34b-132">ASP.NET Core 인증 인증은 다음을 비롯 한 클라이언트 인증서에 대 한 추가 유효성 검사를 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="6a34b-132">ASP.NET Core certification authentication will perform additional validation on a client certificate, including:</span></span>

* <span data-ttu-id="6a34b-133">인증서에 유효한 EKU (확장 키 사용)가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6a34b-133">Certificate has a valid extended key use (EKU)</span></span>
* <span data-ttu-id="6a34b-134">유효 기간 내에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6a34b-134">Is within its validity period</span></span>
* <span data-ttu-id="6a34b-135">인증서 해지 확인</span><span class="sxs-lookup"><span data-stu-id="6a34b-135">Check certificate revocation</span></span>
