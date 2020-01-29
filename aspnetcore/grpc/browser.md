---
title: 브라우저 앱의 gRPC
author: jamesnk
description: GRPC-웹을 사용 하 여 브라우저 앱에서 호출할 수 있도록 ASP.NET Core에서 gRPC 서비스를 구성 하는 방법에 대해 알아봅니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 01/24/2020
uid: grpc/browser
ms.openlocfilehash: 6359c3b76b3cb1ba2b6d9f9a989f64cbf4c4379d
ms.sourcegitcommit: b5ceb0a46d0254cc3425578116e2290142eec0f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/28/2020
ms.locfileid: "76830635"
---
# <a name="grpc-in-browser-apps"></a><span data-ttu-id="1b948-103">브라우저 앱의 gRPC</span><span class="sxs-lookup"><span data-stu-id="1b948-103">gRPC in browser apps</span></span>

<span data-ttu-id="1b948-104">별 [뉴턴-킹](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="1b948-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

> [!IMPORTANT]
> <span data-ttu-id="1b948-105">**gRPC-.NET의 웹 지원이 실험적입니다.**</span><span class="sxs-lookup"><span data-stu-id="1b948-105">**gRPC-Web support in .NET is experimental**</span></span>
>
> <span data-ttu-id="1b948-106">gRPC-웹 .NET은 커밋된 제품이 아니라 실험적 프로젝트입니다.</span><span class="sxs-lookup"><span data-stu-id="1b948-106">gRPC-Web for .NET is an experimental project, not a committed product.</span></span> <span data-ttu-id="1b948-107">다음 작업을 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="1b948-107">We want to:</span></span>
>
> * <span data-ttu-id="1b948-108">GRPC 구현 방법이 적합 한지 테스트 합니다.</span><span class="sxs-lookup"><span data-stu-id="1b948-108">Test that our approach to implementing gRPC-Web works.</span></span>
> * <span data-ttu-id="1b948-109">이 방법이 .NET 개발자에 게 도움이 되는 경우 프록시를 통해 gRPC-웹을 설정 하는 일반적인 방법에 대 한 의견을 받으세요.</span><span class="sxs-lookup"><span data-stu-id="1b948-109">Get feedback on if this approach is useful to .NET developers compared to the traditional way of setting up gRPC-Web via a proxy.</span></span>
>
> <span data-ttu-id="1b948-110">개발자가 사용 하 고 생산성을 높일 수 있도록 [https://github.com/grpc/grpc-dotnet](https://github.com/grpc/grpc-dotnet) 에서 피드백을 남겨 주세요.</span><span class="sxs-lookup"><span data-stu-id="1b948-110">Please leave feedback at [https://github.com/grpc/grpc-dotnet](https://github.com/grpc/grpc-dotnet) to ensure we build something that developers like and are productive with.</span></span>

<span data-ttu-id="1b948-111">브라우저 기반 앱에서 HTTP/2 gRPC 서비스를 호출할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="1b948-111">It is not possible to call a HTTP/2 gRPC service from a browser-based app.</span></span> <span data-ttu-id="1b948-112">[grpc-웹](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md) 은 브라우저 JavaScript 및 Blazor Apps에서 grpc 서비스를 호출할 수 있도록 하는 프로토콜입니다.</span><span class="sxs-lookup"><span data-stu-id="1b948-112">[gRPC-Web](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md) is a protocol that allows browser JavaScript and Blazor apps to call gRPC services.</span></span> <span data-ttu-id="1b948-113">이 문서에서는 .NET Core에서 gRPC-웹을 사용 하는 방법을 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="1b948-113">This article explains how to use gRPC-Web in .NET Core.</span></span>

## <a name="configure-grpc-web-in-aspnet-core"></a><span data-ttu-id="1b948-114">ASP.NET Core에서 gRPC-웹 구성</span><span class="sxs-lookup"><span data-stu-id="1b948-114">Configure gRPC-Web in ASP.NET Core</span></span>

<span data-ttu-id="1b948-115">ASP.NET Core에서 호스트 되는 gRPC 서비스는 HTTP/2 gRPC와 함께 gRPC-웹을 지원 하도록 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1b948-115">gRPC services hosted in ASP.NET Core can be configured to support gRPC-Web alongside HTTP/2 gRPC.</span></span> <span data-ttu-id="1b948-116">gRPC-웹은 서비스를 변경할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="1b948-116">gRPC-Web does not require any changes to services.</span></span> <span data-ttu-id="1b948-117">유일한 수정 사항은 시작 구성입니다.</span><span class="sxs-lookup"><span data-stu-id="1b948-117">The only modification is startup configuration.</span></span>

<span data-ttu-id="1b948-118">ASP.NET Core gRPC 서비스에서 gRPC-웹을 사용 하도록 설정 하려면 다음을 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="1b948-118">To enable gRPC-Web with an ASP.NET Core gRPC service:</span></span>

* <span data-ttu-id="1b948-119">[AspNetCore](https://www.nuget.org/packages/Grpc.AspNetCore.Web) 패키지에 대 한 참조를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="1b948-119">Add a reference to the [Grpc.AspNetCore.Web](https://www.nuget.org/packages/Grpc.AspNetCore.Web) package.</span></span>
* <span data-ttu-id="1b948-120">*Startup.cs*에 `AddGrpcWeb` 및 `UseGrpcWeb`를 추가 하 여 grpc-웹을 사용 하도록 앱을 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="1b948-120">Configure the app to use gRPC-Web by adding `AddGrpcWeb` and `UseGrpcWeb` to *Startup.cs*:</span></span>

[!code-csharp[](~/grpc/browser/sample/Startup.cs?name=snippet_1&highlight=3,10,14)]

<span data-ttu-id="1b948-121">위의 코드:</span><span class="sxs-lookup"><span data-stu-id="1b948-121">The preceding code:</span></span>

* <span data-ttu-id="1b948-122">라우팅 후 및 끝점 앞에 gRPC-웹 미들웨어, `UseGrpcWeb`을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="1b948-122">Adds the gRPC-Web middleware, `UseGrpcWeb`, after routing and before endpoints.</span></span>
* <span data-ttu-id="1b948-123">`EnableGrpcWeb`에서 gRPC-웹을 지 원하는 `endpoints.MapGrpcService<GreeterService>()` 방법을 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="1b948-123">Specifies the `endpoints.MapGrpcService<GreeterService>()` method supports gRPC-Web with `EnableGrpcWeb`.</span></span> 

<span data-ttu-id="1b948-124">또는 ConfigureServices에 `services.AddGrpcWeb(o => o.GrpcWebEnabled = true);`을 추가 하 여 gRPC-웹을 지원 하도록 모든 서비스를 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="1b948-124">Alternatively, configure all services to support gRPC-Web by adding `services.AddGrpcWeb(o => o.GrpcWebEnabled = true);` to ConfigureServices.</span></span>

[!code-csharp[](~/grpc/browser/sample/AllServicesSupportExample_Startup.cs?name=snippet_1&highlight=5,12,16)]

<span data-ttu-id="1b948-125">CORS를 지원 하도록 ASP.NET Core를 구성 하는 등 브라우저에서 gRPC-웹을 호출 하려면 몇 가지 추가 구성이 필요할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1b948-125">Some additional configuration may be required to call gRPC-Web from the browser, such as configuring ASP.NET Core to support CORS.</span></span> <span data-ttu-id="1b948-126">자세한 내용은 [CORS 지원](xref:security/cors)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="1b948-126">For more information, see [support CORS](xref:security/cors).</span></span>

## <a name="call-grpc-web-from-the-browser"></a><span data-ttu-id="1b948-127">브라우저에서 gRPC-웹 호출</span><span class="sxs-lookup"><span data-stu-id="1b948-127">Call gRPC-Web from the browser</span></span>

<span data-ttu-id="1b948-128">브라우저 앱은 gRPC-웹을 사용 하 여 gRPC 서비스를 호출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1b948-128">Browser apps can use gRPC-Web to call gRPC services.</span></span> <span data-ttu-id="1b948-129">브라우저에서 gRPC를 사용 하 여 gRPC 서비스를 호출할 때 몇 가지 요구 사항 및 제한 사항이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1b948-129">There are some requirements and limitations when calling gRPC services with gRPC-Web from the browser:</span></span>

* <span data-ttu-id="1b948-130">서버는 gRPC-웹을 지원 하도록 구성 되어 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="1b948-130">The server must have been configured to support gRPC-Web.</span></span>
* <span data-ttu-id="1b948-131">클라이언트 스트리밍 및 양방향 스트리밍 호출은 지원 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="1b948-131">Client streaming and bidirectional streaming calls aren't supported.</span></span> <span data-ttu-id="1b948-132">서버 스트리밍이 지원 됩니다.</span><span class="sxs-lookup"><span data-stu-id="1b948-132">Server streaming is supported.</span></span>
* <span data-ttu-id="1b948-133">다른 도메인에서 gRPC 서비스를 호출 하려면 서버에 [CORS](xref:security/cors) 를 구성 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="1b948-133">Calling gRPC services on a different domain requires [CORS](xref:security/cors) to be configured on the server.</span></span>

### <a name="javascript-grpc-web-client"></a><span data-ttu-id="1b948-134">JavaScript gRPC-웹 클라이언트</span><span class="sxs-lookup"><span data-stu-id="1b948-134">JavaScript gRPC-Web client</span></span>

<span data-ttu-id="1b948-135">JavaScript gRPC-웹 클라이언트가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1b948-135">There is a JavaScript gRPC-Web client.</span></span> <span data-ttu-id="1b948-136">JavaScript에서 gRPC-Web을 사용 하는 방법에 대 한 지침은 [gRPC를 사용 하 여 javascript 클라이언트 코드 작성-웹](https://github.com/grpc/grpc-web/tree/master/net/grpc/gateway/examples/helloworld#write-client-code)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="1b948-136">For instructions on how to use gRPC-Web from JavaScript, see [write JavaScript client code with gRPC-Web](https://github.com/grpc/grpc-web/tree/master/net/grpc/gateway/examples/helloworld#write-client-code).</span></span>

### <a name="configure-grpc-web-with-the-net-grpc-client"></a><span data-ttu-id="1b948-137">.NET gRPC 클라이언트를 사용 하 여 gRPC-웹 구성</span><span class="sxs-lookup"><span data-stu-id="1b948-137">Configure gRPC-Web with the .NET gRPC client</span></span>

<span data-ttu-id="1b948-138">.NET gRPC 클라이언트는 gRPC-웹 호출을 만들도록 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1b948-138">The .NET gRPC client can be configured to make gRPC-Web calls.</span></span> <span data-ttu-id="1b948-139">이는 브라우저에서 호스트 되 고 JavaScript 코드와 동일한 HTTP 제한이 있는 [Blazor Weasembomapps](xref:blazor/index#blazor-webassembly) 에 유용 합니다.</span><span class="sxs-lookup"><span data-stu-id="1b948-139">This is useful for [Blazor WebAssembly](xref:blazor/index#blazor-webassembly) apps, which are hosted in the browser and have the same HTTP limitations of JavaScript code.</span></span> <span data-ttu-id="1b948-140">.NET 클라이언트를 사용 하 여 gRPC-웹을 호출 하는 것은 [HTTP/2 gRPC와 동일 합니다](xref:grpc/client).</span><span class="sxs-lookup"><span data-stu-id="1b948-140">Calling gRPC-Web with a .NET client is [the same as HTTP/2 gRPC](xref:grpc/client).</span></span> <span data-ttu-id="1b948-141">채널을 만드는 방법만 수정 하면 됩니다.</span><span class="sxs-lookup"><span data-stu-id="1b948-141">The only modification is how the channel is created.</span></span>

<span data-ttu-id="1b948-142">GRPC-웹을 사용 하려면:</span><span class="sxs-lookup"><span data-stu-id="1b948-142">To use gRPC-Web:</span></span>

* <span data-ttu-id="1b948-143">[Grpc .net. 클라이언트 웹](https://www.nuget.org/packages/Grpc.Net.Client.Web) 패키지에 대 한 참조를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="1b948-143">Add a reference to the [Grpc.Net.Client.Web](https://www.nuget.org/packages/Grpc.Net.Client.Web) package.</span></span>
* <span data-ttu-id="1b948-144">`GrpcWebHandler`를 사용 하도록 채널을 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="1b948-144">Configure the channel to use the `GrpcWebHandler`:</span></span>

[!code-csharp[](~/grpc/browser/sample/Handler.cs?name=snippet_1)]

<span data-ttu-id="1b948-145">위의 코드:</span><span class="sxs-lookup"><span data-stu-id="1b948-145">The preceding code:</span></span>

* <span data-ttu-id="1b948-146">GRPC-웹을 사용 하도록 채널을 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="1b948-146">Configures a channel to use gRPC-Web.</span></span>
* <span data-ttu-id="1b948-147">클라이언트를 만들고 채널을 사용 하 여 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="1b948-147">Creates a client and makes a call using the channel.</span></span>

<span data-ttu-id="1b948-148">`GrpcWebHandler`은 만들 때 다음 구성 옵션을 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="1b948-148">The `GrpcWebHandler` has the following configuration options when created:</span></span>

* <span data-ttu-id="1b948-149">**Innerhandler**: HTTP 호출을 수행 하는 기본 <xref:System.Net.Http.HttpMessageHandler> (예: `HttpClientHandler`).</span><span class="sxs-lookup"><span data-stu-id="1b948-149">**InnerHandler**: The underlying <xref:System.Net.Http.HttpMessageHandler> that makes the HTTP call, for example, `HttpClientHandler`.</span></span>
* <span data-ttu-id="1b948-150">**모드**: 열거형을 `GrpcWebMode` 합니다.</span><span class="sxs-lookup"><span data-stu-id="1b948-150">**Mode**: `GrpcWebMode` enum.</span></span> <span data-ttu-id="1b948-151">`GrpcWebMode.GrpcWebText`은 서버 스트리밍 호출을 지 원하는 데 필요한 base64 인코딩로 콘텐츠를 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="1b948-151">`GrpcWebMode.GrpcWebText` configures content to be base64 encoded, which is required to support server streaming calls.</span></span>
* <span data-ttu-id="1b948-152">**HttpVersion**: HTTP 프로토콜 `Version`입니다.</span><span class="sxs-lookup"><span data-stu-id="1b948-152">**HttpVersion**: HTTP protocol `Version`.</span></span> <span data-ttu-id="1b948-153">gRPC-웹은 특정 프로토콜을 요구 하지 않으며 구성 되지 않은 한 요청을 만들 때 지정 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="1b948-153">gRPC-Web doesn't require a specific protocol and won't specify one when making a request unless configured.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="1b948-154">추가 자료</span><span class="sxs-lookup"><span data-stu-id="1b948-154">Additional resources</span></span>

* [<span data-ttu-id="1b948-155">웹 클라이언트용 gRPC GitHub 프로젝트</span><span class="sxs-lookup"><span data-stu-id="1b948-155">gRPC for Web Clients GitHub project</span></span>](https://github.com/grpc/grpc-web)
* <xref:security/cors>
