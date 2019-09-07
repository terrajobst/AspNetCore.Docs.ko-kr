---
title: SignalR과 ASP.NET Core SignalR의 차이점
author: bradygaster
description: SignalR과 ASP.NET Core SignalR의 차이점
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.date: 11/14/2018
uid: signalr/version-differences
ms.openlocfilehash: 70b09493d9b4c96c897465d60e53e93a793c42f9
ms.sourcegitcommit: 387cf29f5d5addef2cbc70670a11d612806b36b2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70746545"
---
# <a name="differences-between-aspnet-signalr-and-aspnet-core-signalr"></a><span data-ttu-id="55b79-103">ASP.NET SignalR과 ASP.NET Core SignalR의 차이점</span><span class="sxs-lookup"><span data-stu-id="55b79-103">Differences between ASP.NET SignalR and ASP.NET Core SignalR</span></span>

<span data-ttu-id="55b79-104">ASP.NET Core SignalR은 ASP.NET SignalR용 클라이언트 또는 서버와 호환되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="55b79-104">ASP.NET Core SignalR isn't compatible with clients or servers for ASP.NET SignalR.</span></span> <span data-ttu-id="55b79-105">이 문서에서는 ASP.NET Core SignalR에서 제거되거나 변경된 기능을 자세히 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="55b79-105">This article details features which have been removed or changed in ASP.NET Core SignalR.</span></span>

## <a name="how-to-identify-the-signalr-version"></a><span data-ttu-id="55b79-106">SignalR 버전 식별 방법</span><span class="sxs-lookup"><span data-stu-id="55b79-106">How to identify the SignalR version</span></span>

|                      | <span data-ttu-id="55b79-107">ASP.NET SignalR</span><span class="sxs-lookup"><span data-stu-id="55b79-107">ASP.NET SignalR</span></span> | <span data-ttu-id="55b79-108">ASP.NET Core SignalR</span><span class="sxs-lookup"><span data-stu-id="55b79-108">ASP.NET Core SignalR</span></span> |
| -------------------- | --------------- | -------------------- |
| <span data-ttu-id="55b79-109">서버 NuGet 패키지</span><span class="sxs-lookup"><span data-stu-id="55b79-109">Server NuGet Package</span></span> | [<span data-ttu-id="55b79-110">Microsoft.AspNet.SignalR</span><span class="sxs-lookup"><span data-stu-id="55b79-110">Microsoft.AspNet.SignalR</span></span>](https://www.nuget.org/packages/Microsoft.AspNet.SignalR/) | <span data-ttu-id="55b79-111">[AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.App/) (.net Core)</span><span class="sxs-lookup"><span data-stu-id="55b79-111">[Microsoft.AspNetCore.App](https://www.nuget.org/packages/Microsoft.AspNetCore.App/) (.NET Core)</span></span><br><span data-ttu-id="55b79-112">[AspNetCore. SignalR](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR/) (.NET Framework)</span><span class="sxs-lookup"><span data-stu-id="55b79-112">[Microsoft.AspNetCore.SignalR](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR/) (.NET Framework)</span></span> |
| <span data-ttu-id="55b79-113">클라이언트 NuGet 패키지</span><span class="sxs-lookup"><span data-stu-id="55b79-113">Client NuGet Packages</span></span> | [<span data-ttu-id="55b79-114">Microsoft.AspNet.SignalR.Client</span><span class="sxs-lookup"><span data-stu-id="55b79-114">Microsoft.AspNet.SignalR.Client</span></span>](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.Client/)<br>[<span data-ttu-id="55b79-115">Microsoft.AspNet.SignalR.JS</span><span class="sxs-lookup"><span data-stu-id="55b79-115">Microsoft.AspNet.SignalR.JS</span></span>](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.JS/) | [<span data-ttu-id="55b79-116">Microsoft.AspNetCore.SignalR.Client</span><span class="sxs-lookup"><span data-stu-id="55b79-116">Microsoft.AspNetCore.SignalR.Client</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/) |
| <span data-ttu-id="55b79-117">클라이언트 npm 패키지</span><span class="sxs-lookup"><span data-stu-id="55b79-117">Client npm Package</span></span> | [<span data-ttu-id="55b79-118">signalr</span><span class="sxs-lookup"><span data-stu-id="55b79-118">signalr</span></span>](https://www.npmjs.com/package/signalr) | [@aspnet/signalr](https://www.npmjs.com/package/@aspnet/signalr) |
| <span data-ttu-id="55b79-119">Java 클라이언트</span><span class="sxs-lookup"><span data-stu-id="55b79-119">Java Client</span></span> | <span data-ttu-id="55b79-120">[GitHub 리포지토리](https://github.com/SignalR/java-client) mapi</span><span class="sxs-lookup"><span data-stu-id="55b79-120">[GitHub Repository](https://github.com/SignalR/java-client) (deprecated)</span></span>  | <span data-ttu-id="55b79-121">Maven package [signalr](https://search.maven.org/artifact/com.microsoft.signalr/signalr)</span><span class="sxs-lookup"><span data-stu-id="55b79-121">Maven package [com.microsoft.signalr](https://search.maven.org/artifact/com.microsoft.signalr/signalr)</span></span> |
| <span data-ttu-id="55b79-122">서버 앱 유형</span><span class="sxs-lookup"><span data-stu-id="55b79-122">Server App Type</span></span> | <span data-ttu-id="55b79-123">ASP.NET (System.web) 또는 OWIN 자체 호스트</span><span class="sxs-lookup"><span data-stu-id="55b79-123">ASP.NET (System.Web) or OWIN Self-Host</span></span> | <span data-ttu-id="55b79-124">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="55b79-124">ASP.NET Core</span></span> |
| <span data-ttu-id="55b79-125">지원되는 서버 플랫폼</span><span class="sxs-lookup"><span data-stu-id="55b79-125">Supported Server Platforms</span></span> | <span data-ttu-id="55b79-126">.NET Framework 4.5 이상</span><span class="sxs-lookup"><span data-stu-id="55b79-126">.NET Framework 4.5 or later</span></span> | <span data-ttu-id="55b79-127">.NET Framework 4.6.1 이상</span><span class="sxs-lookup"><span data-stu-id="55b79-127">.NET Framework 4.6.1 or later</span></span><br><span data-ttu-id="55b79-128">.NET Core 2.1 이상</span><span class="sxs-lookup"><span data-stu-id="55b79-128">.NET Core 2.1 or later</span></span> |

## <a name="feature-differences"></a><span data-ttu-id="55b79-129">기능상의 차이점</span><span class="sxs-lookup"><span data-stu-id="55b79-129">Feature differences</span></span>

### <a name="automatic-reconnects"></a><span data-ttu-id="55b79-130">자동 다시 연결</span><span class="sxs-lookup"><span data-stu-id="55b79-130">Automatic reconnects</span></span>

<span data-ttu-id="55b79-131">ASP.NET Core SignalR은 자동 재연결을 지원하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="55b79-131">Automatic reconnects aren't supported in ASP.NET Core SignalR.</span></span> <span data-ttu-id="55b79-132">클라이언트의 연결이 끊어진 경우 재연결하려면 사용자가 명시적으로 새로운 연결을 시작해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="55b79-132">If the client is disconnected, the user must explicitly start a new connection if they want to reconnect.</span></span> <span data-ttu-id="55b79-133">ASP.NET SignalR은 연결이 끊어지면 SignalR이 서버에 재연결하려고 시도합니다.</span><span class="sxs-lookup"><span data-stu-id="55b79-133">In ASP.NET SignalR, SignalR attempts to reconnect to the server if the connection is dropped.</span></span>

### <a name="protocol-support"></a><span data-ttu-id="55b79-134">프로토콜 지원</span><span class="sxs-lookup"><span data-stu-id="55b79-134">Protocol support</span></span>

<span data-ttu-id="55b79-135">ASP.NET Core SignalR은 JSON뿐만 아니라 [MessagePack](xref:signalr/messagepackhubprotocol) 기반의 새로운 이진 프로토콜을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="55b79-135">ASP.NET Core SignalR supports JSON, as well as a new binary protocol based on [MessagePack](xref:signalr/messagepackhubprotocol).</span></span> <span data-ttu-id="55b79-136">또한 사용자 지정 프로토콜을 만들 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="55b79-136">Additionally, custom protocols can be created.</span></span>

### <a name="transports"></a><span data-ttu-id="55b79-137">전송</span><span class="sxs-lookup"><span data-stu-id="55b79-137">Transports</span></span>

<span data-ttu-id="55b79-138">ASP.NET Core SignalR에서는 무한 프레임 전송이 지원 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="55b79-138">The Forever Frame transport is not supported in ASP.NET Core SignalR.</span></span>

## <a name="differences-on-the-server"></a><span data-ttu-id="55b79-139">서버의 차이점</span><span class="sxs-lookup"><span data-stu-id="55b79-139">Differences on the server</span></span>

<span data-ttu-id="55b79-140">ASP.NET Core SignalR의 서버 쪽 라이브러리는 Razor 및 MVC 프로젝트를 위한 **ASP.NET Core 웹 응용 프로그램** 템플릿의 일부인 [Microsoft.AspNetCore.App 메타패키지](xref:fundamentals/metapackage-app) 패키지에 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="55b79-140">The ASP.NET Core SignalR server-side libraries are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) package that's part of the **ASP.NET Core Web Application** template for both Razor and MVC projects.</span></span>

<span data-ttu-id="55b79-141">ASP.NET Core SignalR은 ASP.NET Core 미들웨어이므로 `Startup.ConfigureServices`에서 [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr)을 호출하여 구성해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="55b79-141">ASP.NET Core SignalR is an ASP.NET Core middleware, so it must be configured by calling [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span>

```csharp
services.AddSignalR()
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="55b79-142">라우팅을 구성 하려면 `Startup.Configure` 메서드의 [useendpoints](/dotnet/api/microsoft.aspnetcore.builder.endpointroutingapplicationbuilderextensions.useendpoints) 메서드 호출 내에서 허브에 경로를 매핑합니다.</span><span class="sxs-lookup"><span data-stu-id="55b79-142">To configure routing, map routes to hubs inside the [UseEndpoints](/dotnet/api/microsoft.aspnetcore.builder.endpointroutingapplicationbuilderextensions.useendpoints) method call in the `Startup.Configure` method.</span></span>


```csharp
app.UseRouting();

app.UseEndpoints(endpoints =>
{
    endpoints.MapHub<ChatHub>("/hub");
});
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="55b79-143">라우팅을 구성하려면 `Startup.Configure` 메서드에서 [UseSignalR](/dotnet/api/microsoft.aspnetcore.builder.signalrappbuilderextensions.usesignalr) 메서드 호출 내부에서 허브에 경로를 매핑합니다.</span><span class="sxs-lookup"><span data-stu-id="55b79-143">To configure routing, map routes to hubs inside the [UseSignalR](/dotnet/api/microsoft.aspnetcore.builder.signalrappbuilderextensions.usesignalr) method call in the `Startup.Configure` method.</span></span>

```csharp
app.UseSignalR(routes =>
{
    routes.MapHub<ChatHub>("/hub");
});
```

::: moniker-end

### <a name="sticky-sessions"></a><span data-ttu-id="55b79-144">고정 세션</span><span class="sxs-lookup"><span data-stu-id="55b79-144">Sticky sessions</span></span>

<span data-ttu-id="55b79-145">ASP.NET SignalR에 대 한 확장 모델을 사용 하면 클라이언트가 팜의 모든 서버에 다시 연결 하 여 메시지를 보낼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="55b79-145">The scaleout model for ASP.NET SignalR allows clients to reconnect and send messages to any server in the farm.</span></span> <span data-ttu-id="55b79-146">ASP.NET Core SignalR에서 클라이언트는 연결 기간 동안 동일한 서버와 상호 작용 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="55b79-146">In ASP.NET Core SignalR, the client must interact with the same server for the duration of the connection.</span></span> <span data-ttu-id="55b79-147">Redis를 사용 하는 확장의 경우에는 고정 세션이 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="55b79-147">For scaleout using Redis, that means sticky sessions are required.</span></span> <span data-ttu-id="55b79-148">[Azure SignalR service](/azure/azure-signalr/)를 사용 하는 확장의 경우 서비스에서 클라이언트에 대 한 연결을 처리 하기 때문에 고정 세션이 필요 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="55b79-148">For scaleout using [Azure SignalR Service](/azure/azure-signalr/), sticky sessions are not required because the service handles connections to clients.</span></span>

### <a name="single-hub-per-connection"></a><span data-ttu-id="55b79-149">연결당 단일 허브</span><span class="sxs-lookup"><span data-stu-id="55b79-149">Single hub per connection</span></span>

<span data-ttu-id="55b79-150">ASP.NET Core SignalR에서는 연결 모델이 단순화되었습니다.</span><span class="sxs-lookup"><span data-stu-id="55b79-150">In ASP.NET Core SignalR, the connection model has been simplified.</span></span> <span data-ttu-id="55b79-151">단일 연결이 여러 허브에 대한 접근을 공유하기 위해 사용되기 보다는 단일 허브에 직접 연결됩니다.</span><span class="sxs-lookup"><span data-stu-id="55b79-151">Connections are made directly to a single hub, rather than a single connection being used to share access to multiple hubs.</span></span>

### <a name="streaming"></a><span data-ttu-id="55b79-152">스트리밍</span><span class="sxs-lookup"><span data-stu-id="55b79-152">Streaming</span></span>

<span data-ttu-id="55b79-153">이제 ASP.NET Core SignalR은 허브에서 클라이언트로의 [스트리밍 데이터](xref:signalr/streaming)를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="55b79-153">ASP.NET Core SignalR now supports [streaming data](xref:signalr/streaming) from the hub to the client.</span></span>

### <a name="state"></a><span data-ttu-id="55b79-154">상태</span><span class="sxs-lookup"><span data-stu-id="55b79-154">State</span></span>

<span data-ttu-id="55b79-155">진행 메시지 관련 기능뿐만 아니라 클라이언트와 허브 간에 임의의 상태를 전달할 수 있는 기능(HubState라고도 함)이 제거되었습니다.</span><span class="sxs-lookup"><span data-stu-id="55b79-155">The ability to pass arbitrary state between clients and the hub (often called HubState) has been removed, as well as support for progress messages.</span></span> <span data-ttu-id="55b79-156">현재 허브 프록시에 해당하는 기능은 존재하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="55b79-156">There is no counterpart of hub proxies at the moment.</span></span>

### <a name="persistentconnection-removal"></a><span data-ttu-id="55b79-157">PersistentConnection 제거</span><span class="sxs-lookup"><span data-stu-id="55b79-157">PersistentConnection removal</span></span>

<span data-ttu-id="55b79-158">ASP.NET Core SignalR에서 [PersistentConnection](https://docs.microsoft.com/previous-versions/aspnet/jj919047(v%3dvs.118)) 클래스가 제거 되었습니다.</span><span class="sxs-lookup"><span data-stu-id="55b79-158">In ASP.NET Core SignalR, the [PersistentConnection](https://docs.microsoft.com/previous-versions/aspnet/jj919047(v%3dvs.118)) class has been removed.</span></span>

### <a name="globalhost"></a><span data-ttu-id="55b79-159">GlobalHost</span><span class="sxs-lookup"><span data-stu-id="55b79-159">GlobalHost</span></span>

<span data-ttu-id="55b79-160">ASP.NET Core에는 프레임 워크에 기본 제공 되는 DI (종속성 주입)가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="55b79-160">ASP.NET Core has dependency injection (DI) built into the framework.</span></span> <span data-ttu-id="55b79-161">서비스는 DI를 사용 하 여 [HubContext](xref:signalr/hubcontext)에 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="55b79-161">Services can use DI to access the [HubContext](xref:signalr/hubcontext).</span></span> <span data-ttu-id="55b79-162">ASP.NET `GlobalHost` SignalR`HubContext` 에 사용 되는 개체는 ASP.NET Core SignalR에 존재 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="55b79-162">The `GlobalHost` object that is used in ASP.NET SignalR to get a `HubContext` doesn't exist in ASP.NET Core SignalR.</span></span>

### <a name="hubpipeline"></a><span data-ttu-id="55b79-163">HubPipeline</span><span class="sxs-lookup"><span data-stu-id="55b79-163">HubPipeline</span></span>

<span data-ttu-id="55b79-164">ASP.NET Core SignalR는 모듈을 `HubPipeline` 지원 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="55b79-164">ASP.NET Core SignalR doesn't have support for `HubPipeline` modules.</span></span>

## <a name="differences-on-the-client"></a><span data-ttu-id="55b79-165">클라이언트의 차이점</span><span class="sxs-lookup"><span data-stu-id="55b79-165">Differences on the client</span></span>

### <a name="typescript"></a><span data-ttu-id="55b79-166">TypeScript</span><span class="sxs-lookup"><span data-stu-id="55b79-166">TypeScript</span></span>

<span data-ttu-id="55b79-167">ASP.NET Core SignalR 클라이언트는 [TypeScript](https://www.typescriptlang.org/)로 작성되었습니다.</span><span class="sxs-lookup"><span data-stu-id="55b79-167">The ASP.NET Core SignalR client is written in [TypeScript](https://www.typescriptlang.org/).</span></span> <span data-ttu-id="55b79-168">[JavaScript 클라이언트](xref:signalr/javascript-client)를 사용할 때 JavaScript 또는 TypeScript로 작성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="55b79-168">You can write in JavaScript or TypeScript when using the [JavaScript client](xref:signalr/javascript-client).</span></span>

### <a name="the-javascript-client-is-hosted-at-npmhttpswwwnpmjscom"></a><span data-ttu-id="55b79-169">JavaScript 클라이언트가 [npm](https://www.npmjs.com/)에서 호스팅됩니다.</span><span class="sxs-lookup"><span data-stu-id="55b79-169">The JavaScript client is hosted at [npm](https://www.npmjs.com/)</span></span>

<span data-ttu-id="55b79-170">이전 버전에서는 Visual Studio의 NuGet 패키지를 통해서 JavaScript 클라이언트를 가져왔습니다.</span><span class="sxs-lookup"><span data-stu-id="55b79-170">In previous versions, the JavaScript client was obtained through a NuGet package in Visual Studio.</span></span> <span data-ttu-id="55b79-171">Core 버전에서는 [@aspnet/signalr](https://www.npmjs.com/package/@aspnet/signalr) npm 패키지에 JavaScript 라이브러리가 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="55b79-171">For the Core versions, the [@aspnet/signalr](https://www.npmjs.com/package/@aspnet/signalr) npm package contains the JavaScript libraries.</span></span> <span data-ttu-id="55b79-172">이 패키지는 **ASP.NET Core 웹 응용 프로그램** 템플릿에 포함되어 있지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="55b79-172">This package isn't included in the **ASP.NET Core Web Application** template.</span></span> <span data-ttu-id="55b79-173">npm을 사용하여 `@aspnet/signalr` npm 패키지를 가져와서 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="55b79-173">Use npm to obtain and install the `@aspnet/signalr` npm package.</span></span>

```console
npm init -y
npm install @aspnet/signalr
```

### <a name="jquery"></a><span data-ttu-id="55b79-174">jQuery</span><span class="sxs-lookup"><span data-stu-id="55b79-174">jQuery</span></span>

<span data-ttu-id="55b79-175">jQuery에 대한 종속성은 제거되었지만 프로젝트에서 여전히 jQuery를 사용할 수는 있습니다.</span><span class="sxs-lookup"><span data-stu-id="55b79-175">The dependency on jQuery has been removed, however projects can still use jQuery.</span></span>

### <a name="internet-explorer-support"></a><span data-ttu-id="55b79-176">Internet Explorer 지원</span><span class="sxs-lookup"><span data-stu-id="55b79-176">Internet Explorer support</span></span>

<span data-ttu-id="55b79-177">ASP.NET Core SignalR에는 Microsoft Internet explorer 11 이상이 필요 합니다 (ASP.NET SignalR에서 지원 되는 Microsoft Internet Explorer 8 이상).</span><span class="sxs-lookup"><span data-stu-id="55b79-177">ASP.NET Core SignalR requires Microsoft Internet Explorer 11 or later (ASP.NET SignalR supported Microsoft Internet Explorer 8 and later).</span></span>

### <a name="javascript-client-method-syntax"></a><span data-ttu-id="55b79-178">JavaScript 클라이언트 메서드 구문</span><span class="sxs-lookup"><span data-stu-id="55b79-178">JavaScript client method syntax</span></span>

<span data-ttu-id="55b79-179">JavaScript 구문이 이전 버전의 SignalR과 달라졌습니다.</span><span class="sxs-lookup"><span data-stu-id="55b79-179">The JavaScript syntax has changed from the previous version of SignalR.</span></span> <span data-ttu-id="55b79-180">`$connection` 개체를 사용하는 대신 [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) API를 사용해서 연결을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="55b79-180">Rather than using the `$connection` object, create a connection using the [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) API.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/hub")
    .build();
```

<span data-ttu-id="55b79-181">[on](/javascript/api/@aspnet/signalr/HubConnection#on) 메서드를 사용해서 허브가 호출할 수 있는 클라이언트 메서드를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="55b79-181">Use the [on](/javascript/api/@aspnet/signalr/HubConnection#on) method to specify client methods that the hub can call.</span></span>

```javascript
connection.on("ReceiveMessage", (user, message) => {
    const msg = message.replace(/&/g, "&amp;").replace(/</g, "&lt;").replace(/>/g, "&gt;");
    const encodedMsg = user + " says " + msg;
    log(encodedMsg);
});
```

<span data-ttu-id="55b79-182">클라이언트 메서드를 생성한 다음, 허브 연결을 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="55b79-182">After creating the client method, start the hub connection.</span></span> <span data-ttu-id="55b79-183">오류를 기록하거나 처리하려면 [catch](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise/catch) 메서드를 연결합니다.</span><span class="sxs-lookup"><span data-stu-id="55b79-183">Chain a [catch](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise/catch) method to log or handle errors.</span></span>

```javascript
connection.start().catch(err => console.error(err.toString()));
```

### <a name="hub-proxies"></a><span data-ttu-id="55b79-184">허브 프록시</span><span class="sxs-lookup"><span data-stu-id="55b79-184">Hub proxies</span></span>

<span data-ttu-id="55b79-185">더 이상 허브 프록시가 자동으로 생성되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="55b79-185">Hub proxies are no longer automatically generated.</span></span> <span data-ttu-id="55b79-186">대신 메서드 이름은 [invoke](/javascript/api/%40aspnet/signalr/hubconnection#invoke) API에 문자열로 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="55b79-186">Instead, the method name is passed into the [invoke](/javascript/api/%40aspnet/signalr/hubconnection#invoke) API as a string.</span></span>

### <a name="net-and-other-clients"></a><span data-ttu-id="55b79-187">.NET 및 기타 클라이언트</span><span class="sxs-lookup"><span data-stu-id="55b79-187">.NET and other clients</span></span>

<span data-ttu-id="55b79-188">`Microsoft.AspNetCore.SignalR.Client` NuGet 패키지에는 ASP.NET Core SignalR용 .NET 클라이언트 라이브러리가 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="55b79-188">The `Microsoft.AspNetCore.SignalR.Client` NuGet package contains the .NET client libraries for ASP.NET Core SignalR.</span></span>

<span data-ttu-id="55b79-189">[HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)를 사용해서 허브에 대한 연결의 인스턴스를 생성하고 빌드합니다.</span><span class="sxs-lookup"><span data-stu-id="55b79-189">Use the [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder) to create and build an instance of a connection to a hub.</span></span>

```csharp
connection = new HubConnectionBuilder()
    .WithUrl("url")
    .Build();
```

## <a name="scaleout-differences"></a><span data-ttu-id="55b79-190">스케일 아웃 차이점</span><span class="sxs-lookup"><span data-stu-id="55b79-190">Scaleout differences</span></span>

<span data-ttu-id="55b79-191">ASP.NET SignalR은 SQL Server 및 Redis를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="55b79-191">ASP.NET SignalR supports SQL Server and Redis.</span></span> <span data-ttu-id="55b79-192">ASP.NET Core SignalR은 Azure SignalR Service 및 Redis를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="55b79-192">ASP.NET Core SignalR supports Azure SignalR Service and Redis.</span></span>

### <a name="aspnet"></a><span data-ttu-id="55b79-193">ASP.NET</span><span class="sxs-lookup"><span data-stu-id="55b79-193">ASP.NET</span></span>

* [<span data-ttu-id="55b79-194">Azure Service Bus를 이용한 SignalR 스케일 아웃</span><span class="sxs-lookup"><span data-stu-id="55b79-194">SignalR scaleout with Azure Service Bus</span></span>](/aspnet/signalr/overview/performance/scaleout-with-windows-azure-service-bus)
* [<span data-ttu-id="55b79-195">Redis를 이용한 SignalR 스케일 아웃</span><span class="sxs-lookup"><span data-stu-id="55b79-195">SignalR scaleout with Redis</span></span>](/aspnet/signalr/overview/performance/scaleout-with-redis)
* [<span data-ttu-id="55b79-196">SQL Server를 이용한 SignalR 스케일 아웃</span><span class="sxs-lookup"><span data-stu-id="55b79-196">SignalR scaleout with SQL Server</span></span>](/aspnet/signalr/overview/performance/scaleout-with-sql-server)

### <a name="aspnet-core"></a><span data-ttu-id="55b79-197">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="55b79-197">ASP.NET Core</span></span>

* [<span data-ttu-id="55b79-198">Azure SignalR Service</span><span class="sxs-lookup"><span data-stu-id="55b79-198">Azure SignalR Service</span></span>](/azure/azure-signalr/)
* [<span data-ttu-id="55b79-199">Redis 백플레인</span><span class="sxs-lookup"><span data-stu-id="55b79-199">Redis Backplane</span></span>](xref:signalr/redis-backplane)

## <a name="additional-resources"></a><span data-ttu-id="55b79-200">추가 자료</span><span class="sxs-lookup"><span data-stu-id="55b79-200">Additional resources</span></span>

* [<span data-ttu-id="55b79-201">허브</span><span class="sxs-lookup"><span data-stu-id="55b79-201">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="55b79-202">JavaScript 클라이언트</span><span class="sxs-lookup"><span data-stu-id="55b79-202">JavaScript client</span></span>](xref:signalr/javascript-client)
* [<span data-ttu-id="55b79-203">.NET 클라이언트</span><span class="sxs-lookup"><span data-stu-id="55b79-203">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="55b79-204">지원되는 플랫폼</span><span class="sxs-lookup"><span data-stu-id="55b79-204">Supported platforms</span></span>](xref:signalr/supported-platforms)
