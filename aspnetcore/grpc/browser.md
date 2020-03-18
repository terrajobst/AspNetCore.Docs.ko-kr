---
title: 브라우저 앱에서 gRPC 사용
author: jamesnk
description: gRPC-Web을 사용하여 브라우저 앱에서 호출할 수 있도록 gRPC 서비스를 ASP.NET Core에서 구성하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 02/16/2020
uid: grpc/browser
ms.openlocfilehash: 3beeffc26ffd3c2dc85bfc22a46d97d5fd78d3d0
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78649419"
---
# <a name="use-grpc-in-browser-apps"></a><span data-ttu-id="667f7-103">브라우저 앱에서 gRPC 사용</span><span class="sxs-lookup"><span data-stu-id="667f7-103">Use gRPC in browser apps</span></span>

<span data-ttu-id="667f7-104">작성자: [James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="667f7-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

> [!IMPORTANT]
> <span data-ttu-id="667f7-105">**.NET의 gRPC-Web 지원은 실험적입니다.**</span><span class="sxs-lookup"><span data-stu-id="667f7-105">**gRPC-Web support in .NET is experimental**</span></span>
>
> <span data-ttu-id="667f7-106">.NET용 gRPC-Web은 커밋된 제품이 아니라 실험적 프로젝트입니다.</span><span class="sxs-lookup"><span data-stu-id="667f7-106">gRPC-Web for .NET is an experimental project, not a committed product.</span></span> <span data-ttu-id="667f7-107">다음 작업을 수행하려고 합니다.</span><span class="sxs-lookup"><span data-stu-id="667f7-107">We want to:</span></span>
>
> * <span data-ttu-id="667f7-108">gRPC-Web 구현 방법이 유효한지 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="667f7-108">Test that our approach to implementing gRPC-Web works.</span></span>
> * <span data-ttu-id="667f7-109">프록시를 통해 gRPC-Web을 설정하는 기존 방법에 비해 이 방법이 .NET 개발자에게 유용한지 아닌지에 대한 피드백을 받습니다.</span><span class="sxs-lookup"><span data-stu-id="667f7-109">Get feedback on if this approach is useful to .NET developers compared to the traditional way of setting up gRPC-Web via a proxy.</span></span>
>
> <span data-ttu-id="667f7-110">개발자가 좋아하고 생산성을 높일 수 있는 제품을 빌드할 수 있도록 [https://github.com/grpc/grpc-dotnet](https://github.com/grpc/grpc-dotnet)에서 피드백을 남겨 주시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="667f7-110">Please leave feedback at [https://github.com/grpc/grpc-dotnet](https://github.com/grpc/grpc-dotnet) to ensure we build something that developers like and are productive with.</span></span>

<span data-ttu-id="667f7-111">브라우저 기반 앱에서 HTTP/2 gRPC 서비스를 호출할 수는 없습니다.</span><span class="sxs-lookup"><span data-stu-id="667f7-111">It is not possible to call a HTTP/2 gRPC service from a browser-based app.</span></span> <span data-ttu-id="667f7-112">[gRPC-Web](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md)은 브라우저 JavaScript 및 Blazor 앱에서 gRPC 서비스를 호출할 수 있게 해주는 프로토콜입니다.</span><span class="sxs-lookup"><span data-stu-id="667f7-112">[gRPC-Web](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md) is a protocol that allows browser JavaScript and Blazor apps to call gRPC services.</span></span> <span data-ttu-id="667f7-113">이 문서에서는 .NET Core에서 gRPC-Web을 사용하는 방법을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="667f7-113">This article explains how to use gRPC-Web in .NET Core.</span></span>

## <a name="configure-grpc-web-in-aspnet-core"></a><span data-ttu-id="667f7-114">ASP.NET Core에서 gRPC-Web 구성</span><span class="sxs-lookup"><span data-stu-id="667f7-114">Configure gRPC-Web in ASP.NET Core</span></span>

<span data-ttu-id="667f7-115">HTTP/2 gRPC와 함께 gRPC-Web을 지원하도록 ASP.NET Core에서 호스트된 gRPC 서비스를 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="667f7-115">gRPC services hosted in ASP.NET Core can be configured to support gRPC-Web alongside HTTP/2 gRPC.</span></span> <span data-ttu-id="667f7-116">gRPC-Web을 사용하기 위해 서비스를 변경할 필요는 없습니다.</span><span class="sxs-lookup"><span data-stu-id="667f7-116">gRPC-Web does not require any changes to services.</span></span> <span data-ttu-id="667f7-117">시작 구성만 수정하면 됩니다.</span><span class="sxs-lookup"><span data-stu-id="667f7-117">The only modification is startup configuration.</span></span>

<span data-ttu-id="667f7-118">ASP.NET Core gRPC 서비스에서 gRPC-Web을 사용하도록 설정하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="667f7-118">To enable gRPC-Web with an ASP.NET Core gRPC service:</span></span>

* <span data-ttu-id="667f7-119">[Grpc.AspNetCore.Web](https://www.nuget.org/packages/Grpc.AspNetCore.Web) 패키지의 참조를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="667f7-119">Add a reference to the [Grpc.AspNetCore.Web](https://www.nuget.org/packages/Grpc.AspNetCore.Web) package.</span></span>
* <span data-ttu-id="667f7-120">*Startup.cs*에 `AddGrpcWeb` 및 `UseGrpcWeb`을 추가하여 gRPC-Web을 사용하도록 앱을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="667f7-120">Configure the app to use gRPC-Web by adding `AddGrpcWeb` and `UseGrpcWeb` to *Startup.cs*:</span></span>

[!code-csharp[](~/grpc/browser/sample/Startup.cs?name=snippet_1&highlight=10,14)]

<span data-ttu-id="667f7-121">위의 코드는</span><span class="sxs-lookup"><span data-stu-id="667f7-121">The preceding code:</span></span>

* <span data-ttu-id="667f7-122">라우팅 이후, 엔드포인트 이전에 gRPC-Web 미들웨어인 `UseGrpcWeb`을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="667f7-122">Adds the gRPC-Web middleware, `UseGrpcWeb`, after routing and before endpoints.</span></span>
* <span data-ttu-id="667f7-123">`EnableGrpcWeb`을 사용하여 `endpoints.MapGrpcService<GreeterService>()` 메서드에서 gRPC-Web을 지원하도록 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="667f7-123">Specifies the `endpoints.MapGrpcService<GreeterService>()` method supports gRPC-Web with `EnableGrpcWeb`.</span></span> 

<span data-ttu-id="667f7-124">또는 ConfigureServices에 `services.AddGrpcWeb(o => o.GrpcWebEnabled = true);`를 추가하여 모든 서비스에서 gRPC-Web을 지원하도록 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="667f7-124">Alternatively, configure all services to support gRPC-Web by adding `services.AddGrpcWeb(o => o.GrpcWebEnabled = true);` to ConfigureServices.</span></span>

[!code-csharp[](~/grpc/browser/sample/AllServicesSupportExample_Startup.cs?name=snippet_1&highlight=6,13)]

### <a name="grpc-web-and-cors"></a><span data-ttu-id="667f7-125">gRPC-Web 및 CORS</span><span class="sxs-lookup"><span data-stu-id="667f7-125">gRPC-Web and CORS</span></span>

<span data-ttu-id="667f7-126">브라우저 보안 때문에 웹 페이지에서 해당 웹 페이지를 제공한 도메인이 아닌 다른 도메인에 요청을 수행할 수는 없습니다.</span><span class="sxs-lookup"><span data-stu-id="667f7-126">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="667f7-127">이 제한은 브라우저 앱에서 gRPC-Web 호출을 수행하는 데에도 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="667f7-127">This restriction applies to making gRPC-Web calls with browser apps.</span></span> <span data-ttu-id="667f7-128">예를 들어 `https://www.contoso.com`에서 제공하는 브라우저 앱은 `https://services.contoso.com`에서 호스트된 gRPC-Web 서비스를 호출할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="667f7-128">For example, a browser app served by `https://www.contoso.com` is blocked from calling gRPC-Web services hosted on `https://services.contoso.com`.</span></span> <span data-ttu-id="667f7-129">CORS(원본 간 리소스 공유)를 사용하여 이 제한을 완화할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="667f7-129">Cross Origin Resource Sharing (CORS) can be used to relax this restriction.</span></span>

<span data-ttu-id="667f7-130">브라우저 앱에서 원본 간 gRPC-Web 호출을 수행할 수 있게 하려면 [ASP.NET Core에서 CORS](xref:security/cors)를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="667f7-130">To allow your browser app to make cross-origin gRPC-Web calls, set up [CORS in ASP.NET Core](xref:security/cors).</span></span> <span data-ttu-id="667f7-131">기본 제공 CORS 지원을 사용하고, <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>를 통해 gRPC 특정 헤더를 공개합니다.</span><span class="sxs-lookup"><span data-stu-id="667f7-131">Use the built-in CORS support, and expose gRPC-specific headers with <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>.</span></span>

[!code-csharp[](~/grpc/browser/sample/CORS_Startup.cs?name=snippet_1&highlight=5-11,19,24)]

<span data-ttu-id="667f7-132">위의 코드는</span><span class="sxs-lookup"><span data-stu-id="667f7-132">The preceding code:</span></span>

* <span data-ttu-id="667f7-133">`AddCors`를 호출하여 CORS 서비스를 추가하고 gRPC 특정 헤더를 공개하는 CORS 정책을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="667f7-133">Calls `AddCors` to add CORS services and configures a CORS policy that exposes gRPC-specific headers.</span></span>
* <span data-ttu-id="667f7-134">`UseCors`를 호출하여 라우팅 이후, 엔드포인트 이전에 CORS 미들웨어를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="667f7-134">Calls `UseCors` to add the CORS middleware after routing and before endpoints.</span></span>
* <span data-ttu-id="667f7-135">`RequiresCors`를 사용하여 `endpoints.MapGrpcService<GreeterService>()` 메서드에서 CORS를 지원하도록 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="667f7-135">Specifies the `endpoints.MapGrpcService<GreeterService>()` method supports CORS with `RequiresCors`.</span></span>

## <a name="call-grpc-web-from-the-browser"></a><span data-ttu-id="667f7-136">브라우저에서 gRPC-Web 호출</span><span class="sxs-lookup"><span data-stu-id="667f7-136">Call gRPC-Web from the browser</span></span>

<span data-ttu-id="667f7-137">브라우저 앱은 gRPC-Web을 사용하여 gRPC 서비스를 호출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="667f7-137">Browser apps can use gRPC-Web to call gRPC services.</span></span> <span data-ttu-id="667f7-138">브라우저에서 gRPC-Web을 사용하여 gRPC 서비스를 호출하는 경우 다음과 같은 몇 가지 요구 사항과 제한 사항이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="667f7-138">There are some requirements and limitations when calling gRPC services with gRPC-Web from the browser:</span></span>

* <span data-ttu-id="667f7-139">서버가 gRPC-Web을 지원하도록 구성되어 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="667f7-139">The server must have been configured to support gRPC-Web.</span></span>
* <span data-ttu-id="667f7-140">클라이언트 스트리밍 및 양방향 스트리밍 호출이 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="667f7-140">Client streaming and bidirectional streaming calls aren't supported.</span></span> <span data-ttu-id="667f7-141">서버 스트리밍이 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="667f7-141">Server streaming is supported.</span></span>
* <span data-ttu-id="667f7-142">다른 도메인에 있는 gRPC 서비스를 호출하려면 서버에서 [CORS](xref:security/cors)를 구성해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="667f7-142">Calling gRPC services on a different domain requires [CORS](xref:security/cors) to be configured on the server.</span></span>

### <a name="javascript-grpc-web-client"></a><span data-ttu-id="667f7-143">JavaScript gRPC-Web 클라이언트</span><span class="sxs-lookup"><span data-stu-id="667f7-143">JavaScript gRPC-Web client</span></span>

<span data-ttu-id="667f7-144">JavaScript gRPC-Web 클라이언트가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="667f7-144">There is a JavaScript gRPC-Web client.</span></span> <span data-ttu-id="667f7-145">JavaScript에서 gRPC-Web을 사용하는 방법에 대한 자세한 내용은 [gRPC-Web을 사용하여 JavaScript 클라이언트 코드 작성](https://github.com/grpc/grpc-web/tree/master/net/grpc/gateway/examples/helloworld#write-client-code)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="667f7-145">For instructions on how to use gRPC-Web from JavaScript, see [write JavaScript client code with gRPC-Web](https://github.com/grpc/grpc-web/tree/master/net/grpc/gateway/examples/helloworld#write-client-code).</span></span>

### <a name="configure-grpc-web-with-the-net-grpc-client"></a><span data-ttu-id="667f7-146">.NET gRPC 클라이언트를 사용하여 gRPC-Web 구성</span><span class="sxs-lookup"><span data-stu-id="667f7-146">Configure gRPC-Web with the .NET gRPC client</span></span>

<span data-ttu-id="667f7-147">gRPC-Web 호출을 수행하도록 .NET gRPC 클라이언트를 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="667f7-147">The .NET gRPC client can be configured to make gRPC-Web calls.</span></span> <span data-ttu-id="667f7-148">이 기능은 브라우저에서 호스트되고 JavaScript 코드와 동일한 HTTP 제한 사항이 있는 [Blazor WebAssembly](xref:blazor/index#blazor-webassembly) 앱에 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="667f7-148">This is useful for [Blazor WebAssembly](xref:blazor/index#blazor-webassembly) apps, which are hosted in the browser and have the same HTTP limitations of JavaScript code.</span></span> <span data-ttu-id="667f7-149">.NET 클라이언트를 사용하여 gRPC-Web을 호출하는 것은 [HTTP/2 gRPC와 동일](xref:grpc/client)합니다.</span><span class="sxs-lookup"><span data-stu-id="667f7-149">Calling gRPC-Web with a .NET client is [the same as HTTP/2 gRPC](xref:grpc/client).</span></span> <span data-ttu-id="667f7-150">채널을 만드는 방법만 수정하면 됩니다.</span><span class="sxs-lookup"><span data-stu-id="667f7-150">The only modification is how the channel is created.</span></span>

<span data-ttu-id="667f7-151">gRPC-Web을 사용하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="667f7-151">To use gRPC-Web:</span></span>

* <span data-ttu-id="667f7-152">[Grpc.Net.Client.Web](https://www.nuget.org/packages/Grpc.Net.Client.Web) 패키지의 참조를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="667f7-152">Add a reference to the [Grpc.Net.Client.Web](https://www.nuget.org/packages/Grpc.Net.Client.Web) package.</span></span>
* <span data-ttu-id="667f7-153">[Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) 패키지의 참조가 2.27.0 이상인지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="667f7-153">Ensure the reference to [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) package is 2.27.0 or greater.</span></span>
* <span data-ttu-id="667f7-154">`GrpcWebHandler`를 사용하도록 채널을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="667f7-154">Configure the channel to use the `GrpcWebHandler`:</span></span>

[!code-csharp[](~/grpc/browser/sample/Handler.cs?name=snippet_1)]

<span data-ttu-id="667f7-155">위의 코드는</span><span class="sxs-lookup"><span data-stu-id="667f7-155">The preceding code:</span></span>

* <span data-ttu-id="667f7-156">gRPC-Web을 사용하도록 채널을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="667f7-156">Configures a channel to use gRPC-Web.</span></span>
* <span data-ttu-id="667f7-157">클라이언트를 만들고 채널을 사용하여 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="667f7-157">Creates a client and makes a call using the channel.</span></span>

<span data-ttu-id="667f7-158">`GrpcWebHandler`를 만들 때는 다음과 같은 구성 옵션이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="667f7-158">The `GrpcWebHandler` has the following configuration options when created:</span></span>

* <span data-ttu-id="667f7-159">**InnerHandler**: gRPC HTTP 요청을 수행하는 기본 <xref:System.Net.Http.HttpMessageHandler>입니다(예: `HttpClientHandler`).</span><span class="sxs-lookup"><span data-stu-id="667f7-159">**InnerHandler**: The underlying <xref:System.Net.Http.HttpMessageHandler> that makes the gRPC HTTP request, for example, `HttpClientHandler`.</span></span>
* <span data-ttu-id="667f7-160">**모드**: gRPC HTTP 요청 `Content-Type`이 `application/grpc-web` 또는 `application/grpc-web-text`인지를 지정하는 열거형 형식입니다.</span><span class="sxs-lookup"><span data-stu-id="667f7-160">**Mode**: An enumeration type that specifies whether the gRPC HTTP request request `Content-Type` is `application/grpc-web` or `application/grpc-web-text`.</span></span>
    * <span data-ttu-id="667f7-161">`GrpcWebMode.GrpcWeb`은 인코딩 없이 전송되도록 콘텐츠를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="667f7-161">`GrpcWebMode.GrpcWeb` configures content to be sent without encoding.</span></span> <span data-ttu-id="667f7-162">기본값.</span><span class="sxs-lookup"><span data-stu-id="667f7-162">Default value.</span></span>
    * <span data-ttu-id="667f7-163">`GrpcWebMode.GrpcWebText`는 base64로 인코딩되도록 콘텐츠를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="667f7-163">`GrpcWebMode.GrpcWebText` configures content to be base64 encoded.</span></span> <span data-ttu-id="667f7-164">브라우저에서 서버 스트리밍 호출을 수행하는 데 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="667f7-164">Required for server streaming calls in browsers.</span></span>
* <span data-ttu-id="667f7-165">**HttpVersion**: 기본 gRPC HTTP 요청에 [HttpRequestMessage.Version](xref:System.Net.Http.HttpRequestMessage.Version)을 설정하는 데 사용되는 HTTP 프로토콜 `Version`입니다.</span><span class="sxs-lookup"><span data-stu-id="667f7-165">**HttpVersion**: HTTP protocol `Version` used to set [HttpRequestMessage.Version](xref:System.Net.Http.HttpRequestMessage.Version) on the underlying gRPC HTTP request.</span></span> <span data-ttu-id="667f7-166">gRPC-Web은 특정 버전이 필요하지 않으며, 지정하지 않을 경우 기본값을 재정의하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="667f7-166">gRPC-Web doesn't require a specific version and doesn't override the default unless specified.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="667f7-167">생성된 gRPC 클라이언트에는 단항 메서드를 호출하기 위한 동기 및 비동기 메서드가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="667f7-167">Generated gRPC clients have sync and async methods for calling unary methods.</span></span> <span data-ttu-id="667f7-168">예를 들어 `SayHello`는 동기이고, `SayHelloAsync`는 비동기입니다.</span><span class="sxs-lookup"><span data-stu-id="667f7-168">For example, `SayHello` is sync and `SayHelloAsync` is async.</span></span> <span data-ttu-id="667f7-169">Blazor WebAssembly 앱에서 동기 메서드를 호출하면 앱이 응답하지 않게 됩니다.</span><span class="sxs-lookup"><span data-stu-id="667f7-169">Calling a sync method in a Blazor WebAssembly app will cause the app to become unresponsive.</span></span> <span data-ttu-id="667f7-170">비동기 메서드는 항상 Blazor WebAssembly에서 사용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="667f7-170">Async methods must always be used in Blazor WebAssembly.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="667f7-171">추가 자료</span><span class="sxs-lookup"><span data-stu-id="667f7-171">Additional resources</span></span>

* [<span data-ttu-id="667f7-172">웹 클라이언트용 gRPC GitHub 프로젝트</span><span class="sxs-lookup"><span data-stu-id="667f7-172">gRPC for Web Clients GitHub project</span></span>](https://github.com/grpc/grpc-web)
* <xref:security/cors>
