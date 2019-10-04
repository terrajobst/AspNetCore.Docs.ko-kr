---
title: SignalR 클라이언트 기능
author: bradygaster
description: 다양 한 ASP.NET Core SignalR 클라이언트에서 지 원하는 기능에 대해 알아봅니다.
ms.author: bradyg
ms.custom: mvc
ms.date: 09/18/2019
uid: signalr/client-features
ms.openlocfilehash: 6718722cdbcfae500026fcd429ca6b9de8f0f103
ms.sourcegitcommit: 73e255e846e414821b8cc20ffa3aec946735cd4e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/03/2019
ms.locfileid: "71925348"
---
# <a name="aspnet-core-signalr-client-features"></a><span data-ttu-id="6a4fa-103">ASP.NET Core SignalR 클라이언트 기능</span><span class="sxs-lookup"><span data-stu-id="6a4fa-103">ASP.NET Core SignalR client features</span></span>

## <a name="feature-distribution"></a><span data-ttu-id="6a4fa-104">기능 배포</span><span class="sxs-lookup"><span data-stu-id="6a4fa-104">Feature distribution</span></span>

<span data-ttu-id="6a4fa-105">다음 표에서는 실시간 지원을 제공 하는 클라이언트에 대 한 기능 및 지원을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="6a4fa-105">The table below shows the features and support for the clients that offer real-time support.</span></span> <span data-ttu-id="6a4fa-106">각 기능에 대해이 기능을 지 원하는 *최소* 버전이 나열 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6a4fa-106">For each feature, the *minimum* version supporting this feature is listed.</span></span> <span data-ttu-id="6a4fa-107">버전이 나열 되지 않은 경우에는 기능이 지원 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="6a4fa-107">If no version is listed, the feature isn't supported.</span></span>

| <span data-ttu-id="6a4fa-108">기능</span><span class="sxs-lookup"><span data-stu-id="6a4fa-108">Feature</span></span> | <span data-ttu-id="6a4fa-109">.NET</span><span class="sxs-lookup"><span data-stu-id="6a4fa-109">.NET</span></span> | <span data-ttu-id="6a4fa-110">JavaScript</span><span class="sxs-lookup"><span data-stu-id="6a4fa-110">JavaScript</span></span> | <span data-ttu-id="6a4fa-111">Java</span><span class="sxs-lookup"><span data-stu-id="6a4fa-111">Java</span></span> |
| ---- | :-: | :-: | :-: |
| <span data-ttu-id="6a4fa-112">Azure SignalR 서비스 지원</span><span class="sxs-lookup"><span data-stu-id="6a4fa-112">Azure SignalR Service Support</span></span> |<span data-ttu-id="6a4fa-113">1.0.0</span><span class="sxs-lookup"><span data-stu-id="6a4fa-113">1.0.0</span></span>|<span data-ttu-id="6a4fa-114">1.0.0</span><span class="sxs-lookup"><span data-stu-id="6a4fa-114">1.0.0</span></span>|<span data-ttu-id="6a4fa-115">1.0.0</span><span class="sxs-lookup"><span data-stu-id="6a4fa-115">1.0.0</span></span>|
| [<span data-ttu-id="6a4fa-116">서버-클라이언트 스트리밍</span><span class="sxs-lookup"><span data-stu-id="6a4fa-116">Server-to-client Streaming</span></span>](xref:signalr/streaming)          |<span data-ttu-id="6a4fa-117">1.0.0</span><span class="sxs-lookup"><span data-stu-id="6a4fa-117">1.0.0</span></span>|<span data-ttu-id="6a4fa-118">1.0.0</span><span class="sxs-lookup"><span data-stu-id="6a4fa-118">1.0.0</span></span>|<span data-ttu-id="6a4fa-119">1.0.0</span><span class="sxs-lookup"><span data-stu-id="6a4fa-119">1.0.0</span></span>|
| [<span data-ttu-id="6a4fa-120">클라이언트와 서버 간 스트리밍</span><span class="sxs-lookup"><span data-stu-id="6a4fa-120">Client-to-server Streaming</span></span>](xref:signalr/streaming)          |<span data-ttu-id="6a4fa-121">3.0.0</span><span class="sxs-lookup"><span data-stu-id="6a4fa-121">3.0.0</span></span>|<span data-ttu-id="6a4fa-122">3.0.0</span><span class="sxs-lookup"><span data-stu-id="6a4fa-122">3.0.0</span></span>|<span data-ttu-id="6a4fa-123">3.0.0</span><span class="sxs-lookup"><span data-stu-id="6a4fa-123">3.0.0</span></span>|
| <span data-ttu-id="6a4fa-124">자동 다시 연결 ([.net](/aspnet/core/signalr/dotnet-client?view=aspnetcore-3.0&tabs=visual-studio#handle-lost-connection), [JavaScript](/aspnet/core/signalr/javascript-client?view=aspnetcore-3.0#reconnect-clients))</span><span class="sxs-lookup"><span data-stu-id="6a4fa-124">Automatic Reconnection ([.NET](/aspnet/core/signalr/dotnet-client?view=aspnetcore-3.0&tabs=visual-studio#handle-lost-connection), [JavaScript](/aspnet/core/signalr/javascript-client?view=aspnetcore-3.0#reconnect-clients))</span></span>          |<span data-ttu-id="6a4fa-125">3.0.0</span><span class="sxs-lookup"><span data-stu-id="6a4fa-125">3.0.0</span></span>|<span data-ttu-id="6a4fa-126">3.0.0</span><span class="sxs-lookup"><span data-stu-id="6a4fa-126">3.0.0</span></span>|<span data-ttu-id="6a4fa-127">❌</span><span class="sxs-lookup"><span data-stu-id="6a4fa-127">❌</span></span>|
| <span data-ttu-id="6a4fa-128">Websocket 전송</span><span class="sxs-lookup"><span data-stu-id="6a4fa-128">WebSockets Transport</span></span> |<span data-ttu-id="6a4fa-129">1.0.0</span><span class="sxs-lookup"><span data-stu-id="6a4fa-129">1.0.0</span></span>|<span data-ttu-id="6a4fa-130">1.0.0</span><span class="sxs-lookup"><span data-stu-id="6a4fa-130">1.0.0</span></span>|<span data-ttu-id="6a4fa-131">1.0.0</span><span class="sxs-lookup"><span data-stu-id="6a4fa-131">1.0.0</span></span>|
| <span data-ttu-id="6a4fa-132">서버에서 보낸 이벤트 전송</span><span class="sxs-lookup"><span data-stu-id="6a4fa-132">Server-Sent Events Transport</span></span> |<span data-ttu-id="6a4fa-133">1.0.0</span><span class="sxs-lookup"><span data-stu-id="6a4fa-133">1.0.0</span></span>|<span data-ttu-id="6a4fa-134">1.0.0</span><span class="sxs-lookup"><span data-stu-id="6a4fa-134">1.0.0</span></span>|<span data-ttu-id="6a4fa-135">❌</span><span class="sxs-lookup"><span data-stu-id="6a4fa-135">❌</span></span>|
| <span data-ttu-id="6a4fa-136">긴 폴링 전송</span><span class="sxs-lookup"><span data-stu-id="6a4fa-136">Long Polling Transport</span></span> |<span data-ttu-id="6a4fa-137">1.0.0</span><span class="sxs-lookup"><span data-stu-id="6a4fa-137">1.0.0</span></span>|<span data-ttu-id="6a4fa-138">1.0.0</span><span class="sxs-lookup"><span data-stu-id="6a4fa-138">1.0.0</span></span>|<span data-ttu-id="6a4fa-139">3.0.0</span><span class="sxs-lookup"><span data-stu-id="6a4fa-139">3.0.0</span></span>|
| <span data-ttu-id="6a4fa-140">JSON 허브 프로토콜</span><span class="sxs-lookup"><span data-stu-id="6a4fa-140">JSON Hub Protocol</span></span> |<span data-ttu-id="6a4fa-141">1.0.0</span><span class="sxs-lookup"><span data-stu-id="6a4fa-141">1.0.0</span></span>|<span data-ttu-id="6a4fa-142">1.0.0</span><span class="sxs-lookup"><span data-stu-id="6a4fa-142">1.0.0</span></span>|<span data-ttu-id="6a4fa-143">1.0.0</span><span class="sxs-lookup"><span data-stu-id="6a4fa-143">1.0.0</span></span>|
| <span data-ttu-id="6a4fa-144">MessagePack 허브 프로토콜</span><span class="sxs-lookup"><span data-stu-id="6a4fa-144">MessagePack Hub Protocol</span></span> |<span data-ttu-id="6a4fa-145">1.0.0</span><span class="sxs-lookup"><span data-stu-id="6a4fa-145">1.0.0</span></span>|<span data-ttu-id="6a4fa-146">1.0.0</span><span class="sxs-lookup"><span data-stu-id="6a4fa-146">1.0.0</span></span>|<span data-ttu-id="6a4fa-147">❌</span><span class="sxs-lookup"><span data-stu-id="6a4fa-147">❌</span></span>|

<span data-ttu-id="6a4fa-148">Java 클라이언트에서 자동 다시 연결에 대 한 지원은 [문제 추적기](https://github.com/aspnet/AspNetCore/issues/8711)에서 추적 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6a4fa-148">Support for automatic reconnect in the Java client is tracked in [our issue tracker](https://github.com/aspnet/AspNetCore/issues/8711).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6a4fa-149">추가 자료</span><span class="sxs-lookup"><span data-stu-id="6a4fa-149">Additional resources</span></span>

* [<span data-ttu-id="6a4fa-150">ASP.NET Core SignalR 시작하기</span><span class="sxs-lookup"><span data-stu-id="6a4fa-150">Get started with SignalR for ASP.NET Core</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="6a4fa-151">지원되는 플랫폼</span><span class="sxs-lookup"><span data-stu-id="6a4fa-151">Supported platforms</span></span>](xref:signalr/supported-platforms)
* [<span data-ttu-id="6a4fa-152">허브</span><span class="sxs-lookup"><span data-stu-id="6a4fa-152">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="6a4fa-153">JavaScript 클라이언트</span><span class="sxs-lookup"><span data-stu-id="6a4fa-153">JavaScript client</span></span>](xref:signalr/javascript-client)
