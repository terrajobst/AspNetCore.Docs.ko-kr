---
title: SignalR 클라이언트 기능
author: bradygaster
description: 다양 한 ASP.NET Core SignalR 클라이언트에서 지 원하는 기능에 대해 알아봅니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: bradyg
ms.custom: mvc
ms.date: 09/18/2019
uid: signalr/client-features
ms.openlocfilehash: 2d6759a5484c37aee6db3d22b3127414231605ae
ms.sourcegitcommit: 14b25156e34c82ed0495b4aff5776ac5b1950b5e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/26/2019
ms.locfileid: "71301184"
---
# <a name="aspnet-core-signalr-client-features"></a><span data-ttu-id="6eaf3-103">ASP.NET Core SignalR 클라이언트 기능</span><span class="sxs-lookup"><span data-stu-id="6eaf3-103">ASP.NET Core SignalR client features</span></span>

## <a name="feature-distribution"></a><span data-ttu-id="6eaf3-104">기능 배포</span><span class="sxs-lookup"><span data-stu-id="6eaf3-104">Feature distribution</span></span>

<span data-ttu-id="6eaf3-105">다음 표에서는 실시간 지원을 제공 하는 클라이언트에 대 한 기능 및 지원을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="6eaf3-105">The table below shows the features and support for the clients that offer real-time support.</span></span>

| <span data-ttu-id="6eaf3-106">기능</span><span class="sxs-lookup"><span data-stu-id="6eaf3-106">Feature</span></span> | <span data-ttu-id="6eaf3-107">.NET</span><span class="sxs-lookup"><span data-stu-id="6eaf3-107">.NET</span></span> | <span data-ttu-id="6eaf3-108">JavaScript</span><span class="sxs-lookup"><span data-stu-id="6eaf3-108">JavaScript</span></span> | <span data-ttu-id="6eaf3-109">Java</span><span class="sxs-lookup"><span data-stu-id="6eaf3-109">Java</span></span> |
| ---- | :-: | :-: | :-: |
| <span data-ttu-id="6eaf3-110">Azure SignalR 서비스 지원</span><span class="sxs-lookup"><span data-stu-id="6eaf3-110">Azure SignalR Service Support</span></span> |<span data-ttu-id="6eaf3-111">✔</span><span class="sxs-lookup"><span data-stu-id="6eaf3-111">✔</span></span>|<span data-ttu-id="6eaf3-112">✔</span><span class="sxs-lookup"><span data-stu-id="6eaf3-112">✔</span></span>|<span data-ttu-id="6eaf3-113">✔</span><span class="sxs-lookup"><span data-stu-id="6eaf3-113">✔</span></span>|
| [<span data-ttu-id="6eaf3-114">서버-클라이언트 스트리밍</span><span class="sxs-lookup"><span data-stu-id="6eaf3-114">Server-to-client Streaming</span></span>](xref:signalr/streaming)          |<span data-ttu-id="6eaf3-115">✔</span><span class="sxs-lookup"><span data-stu-id="6eaf3-115">✔</span></span>|<span data-ttu-id="6eaf3-116">✔</span><span class="sxs-lookup"><span data-stu-id="6eaf3-116">✔</span></span>|<span data-ttu-id="6eaf3-117">✔</span><span class="sxs-lookup"><span data-stu-id="6eaf3-117">✔</span></span>|
| [<span data-ttu-id="6eaf3-118">클라이언트와 서버 간 스트리밍</span><span class="sxs-lookup"><span data-stu-id="6eaf3-118">Client-to-server Streaming</span></span>](xref:signalr/streaming)          |<span data-ttu-id="6eaf3-119">✔</span><span class="sxs-lookup"><span data-stu-id="6eaf3-119">✔</span></span>|<span data-ttu-id="6eaf3-120">✔</span><span class="sxs-lookup"><span data-stu-id="6eaf3-120">✔</span></span>|<span data-ttu-id="6eaf3-121">✔</span><span class="sxs-lookup"><span data-stu-id="6eaf3-121">✔</span></span>|
| <span data-ttu-id="6eaf3-122">자동 다시 연결 ([.net](/aspnet/core/signalr/dotnet-client?view=aspnetcore-3.0&tabs=visual-studio#handle-lost-connection), [JavaScript](/aspnet/core/signalr/javascript-client?view=aspnetcore-3.0#reconnect-clients))</span><span class="sxs-lookup"><span data-stu-id="6eaf3-122">Automatic Reconnection ([.NET](/aspnet/core/signalr/dotnet-client?view=aspnetcore-3.0&tabs=visual-studio#handle-lost-connection), [JavaScript](/aspnet/core/signalr/javascript-client?view=aspnetcore-3.0#reconnect-clients))</span></span>          |<span data-ttu-id="6eaf3-123">✔</span><span class="sxs-lookup"><span data-stu-id="6eaf3-123">✔</span></span>|<span data-ttu-id="6eaf3-124">✔</span><span class="sxs-lookup"><span data-stu-id="6eaf3-124">✔</span></span>| |
| <span data-ttu-id="6eaf3-125">Websocket 전송</span><span class="sxs-lookup"><span data-stu-id="6eaf3-125">WebSockets Transport</span></span> |<span data-ttu-id="6eaf3-126">✔</span><span class="sxs-lookup"><span data-stu-id="6eaf3-126">✔</span></span>|<span data-ttu-id="6eaf3-127">✔</span><span class="sxs-lookup"><span data-stu-id="6eaf3-127">✔</span></span>|<span data-ttu-id="6eaf3-128">✔</span><span class="sxs-lookup"><span data-stu-id="6eaf3-128">✔</span></span>|
| <span data-ttu-id="6eaf3-129">서버에서 보낸 이벤트 전송</span><span class="sxs-lookup"><span data-stu-id="6eaf3-129">Server-Sent Events Transport</span></span> |<span data-ttu-id="6eaf3-130">✔</span><span class="sxs-lookup"><span data-stu-id="6eaf3-130">✔</span></span>|<span data-ttu-id="6eaf3-131">✔</span><span class="sxs-lookup"><span data-stu-id="6eaf3-131">✔</span></span>| |
| <span data-ttu-id="6eaf3-132">긴 폴링 전송</span><span class="sxs-lookup"><span data-stu-id="6eaf3-132">Long Polling Transport</span></span> |<span data-ttu-id="6eaf3-133">✔</span><span class="sxs-lookup"><span data-stu-id="6eaf3-133">✔</span></span>|<span data-ttu-id="6eaf3-134">✔</span><span class="sxs-lookup"><span data-stu-id="6eaf3-134">✔</span></span>|<span data-ttu-id="6eaf3-135">✔</span><span class="sxs-lookup"><span data-stu-id="6eaf3-135">✔</span></span>|
| <span data-ttu-id="6eaf3-136">JSON 허브 프로토콜</span><span class="sxs-lookup"><span data-stu-id="6eaf3-136">JSON Hub Protocol</span></span> |<span data-ttu-id="6eaf3-137">✔</span><span class="sxs-lookup"><span data-stu-id="6eaf3-137">✔</span></span>|<span data-ttu-id="6eaf3-138">✔</span><span class="sxs-lookup"><span data-stu-id="6eaf3-138">✔</span></span>|<span data-ttu-id="6eaf3-139">✔</span><span class="sxs-lookup"><span data-stu-id="6eaf3-139">✔</span></span>|
| <span data-ttu-id="6eaf3-140">MessagePack 허브 프로토콜</span><span class="sxs-lookup"><span data-stu-id="6eaf3-140">MessagePack Hub Protocol</span></span> |<span data-ttu-id="6eaf3-141">✔</span><span class="sxs-lookup"><span data-stu-id="6eaf3-141">✔</span></span>|<span data-ttu-id="6eaf3-142">✔</span><span class="sxs-lookup"><span data-stu-id="6eaf3-142">✔</span></span>| |

<span data-ttu-id="6eaf3-143">Java 클라이언트에서 자동 다시 연결에 대 한 지원은 [문제 추적기](https://github.com/aspnet/AspNetCore/issues/8711)에서 추적 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6eaf3-143">Support for automatic reconnect in the Java client is tracked in [our issue tracker](https://github.com/aspnet/AspNetCore/issues/8711).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6eaf3-144">추가 자료</span><span class="sxs-lookup"><span data-stu-id="6eaf3-144">Additional resources</span></span>

* [<span data-ttu-id="6eaf3-145">ASP.NET Core SignalR 시작하기</span><span class="sxs-lookup"><span data-stu-id="6eaf3-145">Get started with SignalR for ASP.NET Core</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="6eaf3-146">지원되는 플랫폼</span><span class="sxs-lookup"><span data-stu-id="6eaf3-146">Supported platforms</span></span>](xref:signalr/supported-platforms)
* [<span data-ttu-id="6eaf3-147">허브</span><span class="sxs-lookup"><span data-stu-id="6eaf3-147">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="6eaf3-148">JavaScript 클라이언트</span><span class="sxs-lookup"><span data-stu-id="6eaf3-148">JavaScript client</span></span>](xref:signalr/javascript-client)
