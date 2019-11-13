---
title: SignalR와 ASP.NET Core의 차이점 SignalR
author: bradygaster
description: SignalR와 ASP.NET Core의 차이점 SignalR
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.date: 11/12/2019
no-loc:
- SignalR
uid: signalr/version-differences
ms.openlocfilehash: 0f644c132b0fcf9a0ecf0ab181791a6477c97f76
ms.sourcegitcommit: 3fc3020961e1289ee5bf5f3c365ce8304d8ebf19
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73963717"
---
# <a name="differences-between-aspnet-opno-locsignalr-and-aspnet-core-opno-locsignalr"></a><span data-ttu-id="59d79-103">ASP.NET SignalR와 ASP.NET Core 간의 차이점 SignalR</span><span class="sxs-lookup"><span data-stu-id="59d79-103">Differences between ASP.NET SignalR and ASP.NET Core SignalR</span></span>

<span data-ttu-id="59d79-104">ASP.NET Core SignalR는 ASP.NET SignalR에 대 한 클라이언트 또는 서버와 호환 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="59d79-104">ASP.NET Core SignalR isn't compatible with clients or servers for ASP.NET SignalR.</span></span> <span data-ttu-id="59d79-105">이 문서에서는 ASP.NET Core SignalR에서 제거 되거나 변경 된 기능에 대해 자세히 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="59d79-105">This article details features which have been removed or changed in ASP.NET Core SignalR.</span></span>

## <a name="how-to-identify-the-opno-locsignalr-version"></a><span data-ttu-id="59d79-106">SignalR 버전을 확인 하는 방법</span><span class="sxs-lookup"><span data-stu-id="59d79-106">How to identify the SignalR version</span></span>

|                      | <span data-ttu-id="59d79-107">ASP.NET SignalR</span><span class="sxs-lookup"><span data-stu-id="59d79-107">ASP.NET SignalR</span></span> | <span data-ttu-id="59d79-108">ASP.NET Core SignalR</span><span class="sxs-lookup"><span data-stu-id="59d79-108">ASP.NET Core SignalR</span></span> |
| -------------------- | --------------- | -------------------- |
| <span data-ttu-id="59d79-109">서버 NuGet 패키지</span><span class="sxs-lookup"><span data-stu-id="59d79-109">Server NuGet Package</span></span> | <span data-ttu-id="59d79-110">[MicrosoftSignalR](https://www.nuget.org/packages/Microsoft.AspNet.SignalR/)</span><span class="sxs-lookup"><span data-stu-id="59d79-110">[Microsoft.AspNet.SignalR](https://www.nuget.org/packages/Microsoft.AspNet.SignalR/)</span></span> | <span data-ttu-id="59d79-111">[AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.App/) (.net Core)</span><span class="sxs-lookup"><span data-stu-id="59d79-111">[Microsoft.AspNetCore.App](https://www.nuget.org/packages/Microsoft.AspNetCore.App/) (.NET Core)</span></span><br><span data-ttu-id="59d79-112">[AspNetCoreSignalR](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR/)</span><span class="sxs-lookup"><span data-stu-id="59d79-112">[Microsoft.AspNetCore.SignalR](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR/)</span></span> <span data-ttu-id="59d79-113">(.NET Framework)</span><span class="sxs-lookup"><span data-stu-id="59d79-113">(.NET Framework)</span></span> |
| <span data-ttu-id="59d79-114">클라이언트 NuGet 패키지</span><span class="sxs-lookup"><span data-stu-id="59d79-114">Client NuGet Packages</span></span> | <span data-ttu-id="59d79-115">[SignalR. 클라이언트로](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.Client/)</span><span class="sxs-lookup"><span data-stu-id="59d79-115">[Microsoft.AspNet.SignalR.Client](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.Client/)</span></span><br><span data-ttu-id="59d79-116">[SignalR. .JS](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.JS/)</span><span class="sxs-lookup"><span data-stu-id="59d79-116">[Microsoft.AspNet.SignalR.JS](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.JS/)</span></span> | <span data-ttu-id="59d79-117">[AspNetCoreSignalR. 클라이언트로](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/)</span><span class="sxs-lookup"><span data-stu-id="59d79-117">[Microsoft.AspNetCore.SignalR.Client](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/)</span></span> |
| <span data-ttu-id="59d79-118">클라이언트 npm 패키지</span><span class="sxs-lookup"><span data-stu-id="59d79-118">Client npm Package</span></span> | [<span data-ttu-id="59d79-119">signalr</span><span class="sxs-lookup"><span data-stu-id="59d79-119">signalr</span></span>](https://www.npmjs.com/package/signalr) | [@aspnet/signalr](https://www.npmjs.com/package/@aspnet/signalr) |
| <span data-ttu-id="59d79-120">Java 클라이언트</span><span class="sxs-lookup"><span data-stu-id="59d79-120">Java Client</span></span> | <span data-ttu-id="59d79-121">[GitHub 리포지토리](https://github.com/SignalR/java-client) (사용 되지 않음)</span><span class="sxs-lookup"><span data-stu-id="59d79-121">[GitHub Repository](https://github.com/SignalR/java-client) (deprecated)</span></span>  | <span data-ttu-id="59d79-122">Maven package [signalr](https://search.maven.org/artifact/com.microsoft.signalr/signalr)</span><span class="sxs-lookup"><span data-stu-id="59d79-122">Maven package [com.microsoft.signalr](https://search.maven.org/artifact/com.microsoft.signalr/signalr)</span></span> |
| <span data-ttu-id="59d79-123">서버 앱 유형</span><span class="sxs-lookup"><span data-stu-id="59d79-123">Server App Type</span></span> | <span data-ttu-id="59d79-124">ASP.NET (System.web) 또는 OWIN 자체 호스트</span><span class="sxs-lookup"><span data-stu-id="59d79-124">ASP.NET (System.Web) or OWIN Self-Host</span></span> | <span data-ttu-id="59d79-125">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="59d79-125">ASP.NET Core</span></span> |
| <span data-ttu-id="59d79-126">지원 되는 서버 플랫폼</span><span class="sxs-lookup"><span data-stu-id="59d79-126">Supported Server Platforms</span></span> | <span data-ttu-id="59d79-127">.NET Framework 4.5 이상</span><span class="sxs-lookup"><span data-stu-id="59d79-127">.NET Framework 4.5 or later</span></span> | <span data-ttu-id="59d79-128">.NET Framework 4.6.1 이상</span><span class="sxs-lookup"><span data-stu-id="59d79-128">.NET Framework 4.6.1 or later</span></span><br><span data-ttu-id="59d79-129">.NET Core 2.1 이상</span><span class="sxs-lookup"><span data-stu-id="59d79-129">.NET Core 2.1 or later</span></span> |

## <a name="feature-differences"></a><span data-ttu-id="59d79-130">기능 차이점</span><span class="sxs-lookup"><span data-stu-id="59d79-130">Feature differences</span></span>

### <a name="automatic-reconnects"></a><span data-ttu-id="59d79-131">자동 다시 연결</span><span class="sxs-lookup"><span data-stu-id="59d79-131">Automatic reconnects</span></span>

<span data-ttu-id="59d79-132">자동 다시 연결은 ASP.NET Core SignalR에서 지원 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="59d79-132">Automatic reconnects aren't supported in ASP.NET Core SignalR.</span></span> <span data-ttu-id="59d79-133">클라이언트의 연결이 끊어지면 사용자는 다시 연결 하려면 새 연결을 명시적으로 시작 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="59d79-133">If the client is disconnected, the user must explicitly start a new connection if they want to reconnect.</span></span> <span data-ttu-id="59d79-134">ASP.NET SignalR에서 SignalR 연결을 삭제 하면 서버에 다시 연결을 시도 합니다.</span><span class="sxs-lookup"><span data-stu-id="59d79-134">In ASP.NET SignalR, SignalR attempts to reconnect to the server if the connection is dropped.</span></span>

### <a name="protocol-support"></a><span data-ttu-id="59d79-135">프로토콜 지원</span><span class="sxs-lookup"><span data-stu-id="59d79-135">Protocol support</span></span>

<span data-ttu-id="59d79-136">ASP.NET Core SignalR는 JSON을 지원 하 고 [MessagePack](xref:signalr/messagepackhubprotocol)을 기반으로 하는 새 이진 프로토콜도 지원 합니다.</span><span class="sxs-lookup"><span data-stu-id="59d79-136">ASP.NET Core SignalR supports JSON, as well as a new binary protocol based on [MessagePack](xref:signalr/messagepackhubprotocol).</span></span> <span data-ttu-id="59d79-137">또한 사용자 지정 프로토콜을 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="59d79-137">Additionally, custom protocols can be created.</span></span>

### <a name="transports"></a><span data-ttu-id="59d79-138">전송</span><span class="sxs-lookup"><span data-stu-id="59d79-138">Transports</span></span>

<span data-ttu-id="59d79-139">ASP.NET Core SignalR에서는 무한 프레임 전송이 지원 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="59d79-139">The Forever Frame transport is not supported in ASP.NET Core SignalR.</span></span>

## <a name="differences-on-the-server"></a><span data-ttu-id="59d79-140">서버의 차이점</span><span class="sxs-lookup"><span data-stu-id="59d79-140">Differences on the server</span></span>

<span data-ttu-id="59d79-141">ASP.NET Core SignalR 서버 쪽 라이브러리는 Razor 및 MVC 프로젝트에 대 한 **ASP.NET Core 웹 응용 프로그램** 템플릿의 일부인 [AspNetCore 메타 패키지](xref:fundamentals/metapackage-app) 패키지에 포함 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="59d79-141">The ASP.NET Core SignalR server-side libraries are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) package that's part of the **ASP.NET Core Web Application** template for both Razor and MVC projects.</span></span>

<span data-ttu-id="59d79-142">ASP.NET Core SignalR ASP.NET Core 미들웨어 이므로 `Startup.ConfigureServices`에서 [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) 를 호출 하 여 구성 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="59d79-142">ASP.NET Core SignalR is an ASP.NET Core middleware, so it must be configured by calling [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span>

```csharp
services.AddSignalR()
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="59d79-143">라우팅을 구성 하려면 `Startup.Configure` 메서드의 [Useendpoints](/dotnet/api/microsoft.aspnetcore.builder.endpointroutingapplicationbuilderextensions.useendpoints) 메서드 호출 내에서 허브에 경로를 매핑합니다.</span><span class="sxs-lookup"><span data-stu-id="59d79-143">To configure routing, map routes to hubs inside the [UseEndpoints](/dotnet/api/microsoft.aspnetcore.builder.endpointroutingapplicationbuilderextensions.useendpoints) method call in the `Startup.Configure` method.</span></span>


```csharp
app.UseRouting();

app.UseEndpoints(endpoints =>
{
    endpoints.MapHub<ChatHub>("/hub");
});
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="59d79-144">라우팅을 구성 하려면 `Startup.Configure` 메서드의 [UseSignalR](/dotnet/api/microsoft.aspnetcore.builder.signalrappbuilderextensions.usesignalr) 메서드 호출 내에서 허브에 경로를 매핑합니다.</span><span class="sxs-lookup"><span data-stu-id="59d79-144">To configure routing, map routes to hubs inside the [UseSignalR](/dotnet/api/microsoft.aspnetcore.builder.signalrappbuilderextensions.usesignalr) method call in the `Startup.Configure` method.</span></span>

```csharp
app.UseSignalR(routes =>
{
    routes.MapHub<ChatHub>("/hub");
});
```

::: moniker-end

### <a name="sticky-sessions"></a><span data-ttu-id="59d79-145">고정 세션</span><span class="sxs-lookup"><span data-stu-id="59d79-145">Sticky sessions</span></span>

<span data-ttu-id="59d79-146">ASP.NET SignalR의 확장 모델을 사용 하면 클라이언트가 팜의 모든 서버에 다시 연결 하 여 메시지를 보낼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="59d79-146">The scaleout model for ASP.NET SignalR allows clients to reconnect and send messages to any server in the farm.</span></span> <span data-ttu-id="59d79-147">ASP.NET Core SignalR에서 클라이언트는 연결 기간 동안 동일한 서버와 상호 작용 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="59d79-147">In ASP.NET Core SignalR, the client must interact with the same server for the duration of the connection.</span></span> <span data-ttu-id="59d79-148">Redis를 사용 하는 확장의 경우에는 고정 세션이 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="59d79-148">For scaleout using Redis, that means sticky sessions are required.</span></span> <span data-ttu-id="59d79-149">[Azure SignalR 서비스](/azure/azure-signalr/)를 사용 하는 확장의 경우 서비스에서 클라이언트에 대 한 연결을 처리 하기 때문에 고정 세션이 필요 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="59d79-149">For scaleout using [Azure SignalR Service](/azure/azure-signalr/), sticky sessions are not required because the service handles connections to clients.</span></span>

### <a name="single-hub-per-connection"></a><span data-ttu-id="59d79-150">연결당 단일 허브</span><span class="sxs-lookup"><span data-stu-id="59d79-150">Single hub per connection</span></span>

<span data-ttu-id="59d79-151">ASP.NET Core SignalR에서는 연결 모델이 간소화 되었습니다.</span><span class="sxs-lookup"><span data-stu-id="59d79-151">In ASP.NET Core SignalR, the connection model has been simplified.</span></span> <span data-ttu-id="59d79-152">여러 허브에 대 한 액세스를 공유 하는 데 사용 되는 단일 연결이 아닌 단일 허브로 직접 연결 됩니다.</span><span class="sxs-lookup"><span data-stu-id="59d79-152">Connections are made directly to a single hub, rather than a single connection being used to share access to multiple hubs.</span></span>

### <a name="streaming"></a><span data-ttu-id="59d79-153">스트리밍</span><span class="sxs-lookup"><span data-stu-id="59d79-153">Streaming</span></span>

<span data-ttu-id="59d79-154">이제 ASP.NET Core SignalR 허브에서 클라이언트로의 [스트리밍 데이터](xref:signalr/streaming) 를 지원 합니다.</span><span class="sxs-lookup"><span data-stu-id="59d79-154">ASP.NET Core SignalR now supports [streaming data](xref:signalr/streaming) from the hub to the client.</span></span>

### <a name="state"></a><span data-ttu-id="59d79-155">상태</span><span class="sxs-lookup"><span data-stu-id="59d79-155">State</span></span>

<span data-ttu-id="59d79-156">클라이언트와 허브 (HubState 라고도 함) 간에 임의의 상태를 전달 하는 기능이 제거 되었으며 진행 메시지를 지원 합니다.</span><span class="sxs-lookup"><span data-stu-id="59d79-156">The ability to pass arbitrary state between clients and the hub (often called HubState) has been removed, as well as support for progress messages.</span></span> <span data-ttu-id="59d79-157">현재 허브 프록시의 대응 항목이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="59d79-157">There is no counterpart of hub proxies at the moment.</span></span>

### <a name="persistentconnection-removal"></a><span data-ttu-id="59d79-158">PersistentConnection 제거</span><span class="sxs-lookup"><span data-stu-id="59d79-158">PersistentConnection removal</span></span>

<span data-ttu-id="59d79-159">ASP.NET Core SignalR에서는 [PersistentConnection](https://docs.microsoft.com/previous-versions/aspnet/jj919047(v%3dvs.118)) 클래스가 제거 되었습니다.</span><span class="sxs-lookup"><span data-stu-id="59d79-159">In ASP.NET Core SignalR, the [PersistentConnection](https://docs.microsoft.com/previous-versions/aspnet/jj919047(v%3dvs.118)) class has been removed.</span></span>

### <a name="globalhost"></a><span data-ttu-id="59d79-160">GlobalHost</span><span class="sxs-lookup"><span data-stu-id="59d79-160">GlobalHost</span></span>

<span data-ttu-id="59d79-161">ASP.NET Core에는 프레임 워크에 기본 제공 되는 DI (종속성 주입)가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="59d79-161">ASP.NET Core has dependency injection (DI) built into the framework.</span></span> <span data-ttu-id="59d79-162">서비스는 DI를 사용 하 여 [HubContext](xref:signalr/hubcontext)에 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="59d79-162">Services can use DI to access the [HubContext](xref:signalr/hubcontext).</span></span> <span data-ttu-id="59d79-163">ASP.NET SignalR에서 `HubContext`을 가져오는 데 사용 되는 `GlobalHost` 개체는 ASP.NET Core SignalR에 존재 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="59d79-163">The `GlobalHost` object that is used in ASP.NET SignalR to get a `HubContext` doesn't exist in ASP.NET Core SignalR.</span></span>

### <a name="hubpipeline"></a><span data-ttu-id="59d79-164">HubPipeline</span><span class="sxs-lookup"><span data-stu-id="59d79-164">HubPipeline</span></span>

<span data-ttu-id="59d79-165">ASP.NET Core SignalR는 `HubPipeline` 모듈을 지원 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="59d79-165">ASP.NET Core SignalR doesn't have support for `HubPipeline` modules.</span></span>

## <a name="differences-on-the-client"></a><span data-ttu-id="59d79-166">클라이언트의 차이점</span><span class="sxs-lookup"><span data-stu-id="59d79-166">Differences on the client</span></span>

### <a name="typescript"></a><span data-ttu-id="59d79-167">TypeScript</span><span class="sxs-lookup"><span data-stu-id="59d79-167">TypeScript</span></span>

<span data-ttu-id="59d79-168">ASP.NET Core SignalR 클라이언트는 [TypeScript](https://www.typescriptlang.org/)로 작성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="59d79-168">The ASP.NET Core SignalR client is written in [TypeScript](https://www.typescriptlang.org/).</span></span> <span data-ttu-id="59d79-169">[Javascript 클라이언트](xref:signalr/javascript-client)를 사용 하는 경우 Javascript 또는 TypeScript로 작성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="59d79-169">You can write in JavaScript or TypeScript when using the [JavaScript client](xref:signalr/javascript-client).</span></span>

### <a name="the-javascript-client-is-hosted-at-npmhttpswwwnpmjscom"></a><span data-ttu-id="59d79-170">JavaScript 클라이언트는 [npm](https://www.npmjs.com/) 에서 호스팅됩니다.</span><span class="sxs-lookup"><span data-stu-id="59d79-170">The JavaScript client is hosted at [npm](https://www.npmjs.com/)</span></span>

<span data-ttu-id="59d79-171">이전 버전에서는 Visual Studio의 NuGet 패키지를 통해 JavaScript 클라이언트를 가져왔습니다.</span><span class="sxs-lookup"><span data-stu-id="59d79-171">In previous versions, the JavaScript client was obtained through a NuGet package in Visual Studio.</span></span> <span data-ttu-id="59d79-172">핵심 버전의 [@aspnet/signalr](https://www.npmjs.com/package/@aspnet/signalr) npm 패키지에는 JavaScript 라이브러리가 포함 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="59d79-172">For the Core versions, the [@aspnet/signalr](https://www.npmjs.com/package/@aspnet/signalr) npm package contains the JavaScript libraries.</span></span> <span data-ttu-id="59d79-173">이 패키지는 **ASP.NET Core 웹 응용 프로그램** 템플릿에 포함 되어 있지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="59d79-173">This package isn't included in the **ASP.NET Core Web Application** template.</span></span> <span data-ttu-id="59d79-174">Npm를 사용 하 여 `@aspnet/signalr` npm 패키지를 다운로드 하 고 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="59d79-174">Use npm to obtain and install the `@aspnet/signalr` npm package.</span></span>

```console
npm init -y
npm install @aspnet/signalr
```

### <a name="jquery"></a><span data-ttu-id="59d79-175">jQuery</span><span class="sxs-lookup"><span data-stu-id="59d79-175">jQuery</span></span>

<span data-ttu-id="59d79-176">JQuery에 대 한 종속성이 제거 되었지만 프로젝트에서 jQuery를 계속 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="59d79-176">The dependency on jQuery has been removed, however projects can still use jQuery.</span></span>

### <a name="internet-explorer-support"></a><span data-ttu-id="59d79-177">Internet Explorer 지원</span><span class="sxs-lookup"><span data-stu-id="59d79-177">Internet Explorer support</span></span>

<span data-ttu-id="59d79-178">ASP.NET Core SignalR를 사용 하려면 Microsoft internet explorer 11 이상이 필요 합니다 (ASP.NET SignalR Microsoft Internet Explorer 8 이상이 지원 됨).</span><span class="sxs-lookup"><span data-stu-id="59d79-178">ASP.NET Core SignalR requires Microsoft Internet Explorer 11 or later (ASP.NET SignalR supported Microsoft Internet Explorer 8 and later).</span></span>

### <a name="javascript-client-method-syntax"></a><span data-ttu-id="59d79-179">JavaScript 클라이언트 메서드 구문</span><span class="sxs-lookup"><span data-stu-id="59d79-179">JavaScript client method syntax</span></span>

<span data-ttu-id="59d79-180">이전 버전의 SignalR에서 JavaScript 구문이 변경 되었습니다.</span><span class="sxs-lookup"><span data-stu-id="59d79-180">The JavaScript syntax has changed from the previous version of SignalR.</span></span> <span data-ttu-id="59d79-181">`$connection` 개체를 사용 하는 대신 [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) API를 사용 하 여 연결을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="59d79-181">Rather than using the `$connection` object, create a connection using the [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) API.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/hub")
    .build();
```

<span data-ttu-id="59d79-182">[On](/javascript/api/@aspnet/signalr/HubConnection#on) 메서드를 사용 하 여 허브에서 호출할 수 있는 클라이언트 메서드를 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="59d79-182">Use the [on](/javascript/api/@aspnet/signalr/HubConnection#on) method to specify client methods that the hub can call.</span></span>

```javascript
connection.on("ReceiveMessage", (user, message) => {
    const msg = message.replace(/&/g, "&amp;").replace(/</g, "&lt;").replace(/>/g, "&gt;");
    const encodedMsg = user + " says " + msg;
    log(encodedMsg);
});
```

<span data-ttu-id="59d79-183">클라이언트 메서드를 만든 후 허브 연결을 시작 합니다.</span><span class="sxs-lookup"><span data-stu-id="59d79-183">After creating the client method, start the hub connection.</span></span> <span data-ttu-id="59d79-184">[Catch](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise/catch) 메서드를 연결 하 여 오류를 기록 하거나 처리 합니다.</span><span class="sxs-lookup"><span data-stu-id="59d79-184">Chain a [catch](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise/catch) method to log or handle errors.</span></span>

```javascript
connection.start().catch(err => console.error(err.toString()));
```

### <a name="hub-proxies"></a><span data-ttu-id="59d79-185">허브 프록시</span><span class="sxs-lookup"><span data-stu-id="59d79-185">Hub proxies</span></span>

<span data-ttu-id="59d79-186">허브 프록시는 더 이상 자동으로 생성 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="59d79-186">Hub proxies are no longer automatically generated.</span></span> <span data-ttu-id="59d79-187">대신 메서드 이름이 [호출](/javascript/api/%40aspnet/signalr/hubconnection#invoke) API에 문자열로 전달 됩니다.</span><span class="sxs-lookup"><span data-stu-id="59d79-187">Instead, the method name is passed into the [invoke](/javascript/api/%40aspnet/signalr/hubconnection#invoke) API as a string.</span></span>

### <a name="net-and-other-clients"></a><span data-ttu-id="59d79-188">.NET 및 기타 클라이언트</span><span class="sxs-lookup"><span data-stu-id="59d79-188">.NET and other clients</span></span>

<span data-ttu-id="59d79-189">`Microsoft.AspNetCore.SignalR.Client` NuGet 패키지에는 ASP.NET Core SignalR에 대 한 .NET 클라이언트 라이브러리가 포함 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="59d79-189">The `Microsoft.AspNetCore.SignalR.Client` NuGet package contains the .NET client libraries for ASP.NET Core SignalR.</span></span>

<span data-ttu-id="59d79-190">[HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder) 를 사용 하 여 허브에 대 한 연결의 인스턴스를 만들고 빌드합니다.</span><span class="sxs-lookup"><span data-stu-id="59d79-190">Use the [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder) to create and build an instance of a connection to a hub.</span></span>

```csharp
connection = new HubConnectionBuilder()
    .WithUrl("url")
    .Build();
```

## <a name="scaleout-differences"></a><span data-ttu-id="59d79-191">확장 차이점</span><span class="sxs-lookup"><span data-stu-id="59d79-191">Scaleout differences</span></span>

<span data-ttu-id="59d79-192">ASP.NET SignalR는 SQL Server 및 Redis를 지원 합니다.</span><span class="sxs-lookup"><span data-stu-id="59d79-192">ASP.NET SignalR supports SQL Server and Redis.</span></span> <span data-ttu-id="59d79-193">ASP.NET Core SignalR는 Azure SignalR 서비스 및 Redis을 지원 합니다.</span><span class="sxs-lookup"><span data-stu-id="59d79-193">ASP.NET Core SignalR supports Azure SignalR Service and Redis.</span></span>

### <a name="aspnet"></a><span data-ttu-id="59d79-194">ASP.NET</span><span class="sxs-lookup"><span data-stu-id="59d79-194">ASP.NET</span></span>

* <span data-ttu-id="59d79-195">[Azure Service Bus를 사용 하 여 확장 SignalR](/aspnet/signalr/overview/performance/scaleout-with-windows-azure-service-bus)</span><span class="sxs-lookup"><span data-stu-id="59d79-195">[SignalR scaleout with Azure Service Bus](/aspnet/signalr/overview/performance/scaleout-with-windows-azure-service-bus)</span></span>
* <span data-ttu-id="59d79-196">[Redis를 사용 하 여 확장 SignalR](/aspnet/signalr/overview/performance/scaleout-with-redis)</span><span class="sxs-lookup"><span data-stu-id="59d79-196">[SignalR scaleout with Redis](/aspnet/signalr/overview/performance/scaleout-with-redis)</span></span>
* <span data-ttu-id="59d79-197">[SQL Server를 사용 하 여 확장 SignalR](/aspnet/signalr/overview/performance/scaleout-with-sql-server)</span><span class="sxs-lookup"><span data-stu-id="59d79-197">[SignalR scaleout with SQL Server](/aspnet/signalr/overview/performance/scaleout-with-sql-server)</span></span>

### <a name="aspnet-core"></a><span data-ttu-id="59d79-198">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="59d79-198">ASP.NET Core</span></span>

* <span data-ttu-id="59d79-199">[Azure SignalR 서비스](/azure/azure-signalr/)</span><span class="sxs-lookup"><span data-stu-id="59d79-199">[Azure SignalR Service](/azure/azure-signalr/)</span></span>
* [<span data-ttu-id="59d79-200">Redis 백플레인</span><span class="sxs-lookup"><span data-stu-id="59d79-200">Redis Backplane</span></span>](xref:signalr/redis-backplane)

## <a name="additional-resources"></a><span data-ttu-id="59d79-201">추가 자료</span><span class="sxs-lookup"><span data-stu-id="59d79-201">Additional resources</span></span>

* [<span data-ttu-id="59d79-202">허브</span><span class="sxs-lookup"><span data-stu-id="59d79-202">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="59d79-203">JavaScript 클라이언트</span><span class="sxs-lookup"><span data-stu-id="59d79-203">JavaScript client</span></span>](xref:signalr/javascript-client)
* [<span data-ttu-id="59d79-204">.NET 클라이언트</span><span class="sxs-lookup"><span data-stu-id="59d79-204">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="59d79-205">지원되는 플랫폼</span><span class="sxs-lookup"><span data-stu-id="59d79-205">Supported platforms</span></span>](xref:signalr/supported-platforms)
