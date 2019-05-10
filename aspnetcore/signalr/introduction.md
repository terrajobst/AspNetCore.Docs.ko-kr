---
title: ASP.NET Core SignalR 소개
author: bradygaster
description: ASP.NET Core SignalR 라이브러리로 앱에 실시간 기능을 추가하는 방법에 대해 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/25/2018
uid: signalr/introduction
ms.openlocfilehash: 673efafce60dfa46cb99f9537fda2bca42bf9822
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64892250"
---
# <a name="introduction-to-aspnet-core-signalr"></a><span data-ttu-id="4b1fa-103">ASP.NET Core SignalR 소개</span><span class="sxs-lookup"><span data-stu-id="4b1fa-103">Introduction to ASP.NET Core SignalR</span></span>

## <a name="what-is-signalr"></a><span data-ttu-id="4b1fa-104">SignalR이란?</span><span class="sxs-lookup"><span data-stu-id="4b1fa-104">What is SignalR?</span></span>

<span data-ttu-id="4b1fa-105">ASP.NET Core SignalR은 앱에 실시간 웹 기능을 손쉽게 추가할 수 있는 오픈 소스 라이브러리입니다.</span><span class="sxs-lookup"><span data-stu-id="4b1fa-105">ASP.NET Core SignalR is an open-source library that simplifies adding real-time web functionality to apps.</span></span> <span data-ttu-id="4b1fa-106">실시간 웹 기능을 사용하면 서버 쪽 코드에서 클라이언트로 콘텐츠를 즉시 푸시할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4b1fa-106">Real-time web functionality enables server-side code to push content to clients instantly.</span></span>

<span data-ttu-id="4b1fa-107">SignalR은 다음과 같은 앱에 적합합니다.</span><span class="sxs-lookup"><span data-stu-id="4b1fa-107">Good candidates for SignalR:</span></span>

* <span data-ttu-id="4b1fa-108">서버로부터 빈번한 업데이트가 필요한 앱.</span><span class="sxs-lookup"><span data-stu-id="4b1fa-108">Apps that require high frequency updates from the server.</span></span> <span data-ttu-id="4b1fa-109">게임, 소셜 네트워크, 투표, 경매, 지도 및 GPS 앱을 예로 들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4b1fa-109">Examples are gaming, social networks, voting, auction, maps, and GPS apps.</span></span>
* <span data-ttu-id="4b1fa-110">대시보드 및 모니터링 앱.</span><span class="sxs-lookup"><span data-stu-id="4b1fa-110">Dashboards and monitoring apps.</span></span> <span data-ttu-id="4b1fa-111">예를 들어 기업 대시보드, 즉각적인 매출 업데이트나 여행 경고가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4b1fa-111">Examples include company dashboards, instant sales updates, or travel alerts.</span></span>
* <span data-ttu-id="4b1fa-112">공동 작업 앱.</span><span class="sxs-lookup"><span data-stu-id="4b1fa-112">Collaborative apps.</span></span> <span data-ttu-id="4b1fa-113">화이트 보드 앱 및 팀 회의 소프트웨어가 공동 작업 앱의 예입니다.</span><span class="sxs-lookup"><span data-stu-id="4b1fa-113">Whiteboard apps and team meeting software are examples of collaborative apps.</span></span>
* <span data-ttu-id="4b1fa-114">알림이 필요한 앱.</span><span class="sxs-lookup"><span data-stu-id="4b1fa-114">Apps that require notifications.</span></span> <span data-ttu-id="4b1fa-115">소셜 네트워크, 이메일, 채팅, 게임, 여행 경고 및 다른 많은 앱들이 알림을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="4b1fa-115">Social networks, email, chat, games, travel alerts, and many other apps use notifications.</span></span>

<span data-ttu-id="4b1fa-116">SignalR은 서버에서 클라이언트로 [원격 프로시저 호출(RPC)](https://wikipedia.org/wiki/Remote_procedure_call)을 생성하는 API를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="4b1fa-116">SignalR provides an API for creating server-to-client [remote procedure calls (RPC)](https://wikipedia.org/wiki/Remote_procedure_call).</span></span> <span data-ttu-id="4b1fa-117">이 RPC는 서버 쪽 .NET Core 코드에서 클라이언트 상의 JavaScript 함수를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="4b1fa-117">The RPCs call JavaScript functions on clients from server-side .NET Core code.</span></span>

<span data-ttu-id="4b1fa-118">다음은 ASP.NET Core SignalR의 일부 기능입니다.</span><span class="sxs-lookup"><span data-stu-id="4b1fa-118">Here are some features of SignalR for ASP.NET Core:</span></span>

* <span data-ttu-id="4b1fa-119">연결 관리를 자동으로 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="4b1fa-119">Handles connection management automatically.</span></span>
* <span data-ttu-id="4b1fa-120">연결된 모든 클라이언트에 동시에 메시지를 전송합니다.</span><span class="sxs-lookup"><span data-stu-id="4b1fa-120">Sends messages to all connected clients simultaneously.</span></span> <span data-ttu-id="4b1fa-121">그 예로 대화방을 들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4b1fa-121">For example, a chat room.</span></span>
* <span data-ttu-id="4b1fa-122">특정 클라이언트 또는 클라이언트 그룹에 메시지를 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="4b1fa-122">Sends messages to specific clients or groups of clients.</span></span>
* <span data-ttu-id="4b1fa-123">트래픽 증가를 처리할 수 있도록 확장됩니다.</span><span class="sxs-lookup"><span data-stu-id="4b1fa-123">Scales to handle increasing traffic.</span></span>

<span data-ttu-id="4b1fa-124">소스는 [GitHub의 SignalR 리포지토리](https://github.com/aspnet/AspNetCore/tree/master/src/SignalR)에서 호스트됩니다.</span><span class="sxs-lookup"><span data-stu-id="4b1fa-124">The source is hosted in a [SignalR repository on GitHub](https://github.com/aspnet/AspNetCore/tree/master/src/SignalR).</span></span>

## <a name="transports"></a><span data-ttu-id="4b1fa-125">전송</span><span class="sxs-lookup"><span data-stu-id="4b1fa-125">Transports</span></span>

<span data-ttu-id="4b1fa-126">SignalR은 실시간 통신을 처리하기 위한 몇 가지 기법을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="4b1fa-126">SignalR supports several techniques for handling real-time communications:</span></span>

* [<span data-ttu-id="4b1fa-127">WebSockets</span><span class="sxs-lookup"><span data-stu-id="4b1fa-127">WebSockets</span></span>](https://tools.ietf.org/html/rfc7118)
* <span data-ttu-id="4b1fa-128">서버에서 전송 이벤트</span><span class="sxs-lookup"><span data-stu-id="4b1fa-128">Server-Sent Events</span></span>
* <span data-ttu-id="4b1fa-129">긴 폴링</span><span class="sxs-lookup"><span data-stu-id="4b1fa-129">Long Polling</span></span>

<span data-ttu-id="4b1fa-130">SignalR은 서버와 클라이언트의 기능을 감안하여 자동으로 가장 적합한 전송 방식을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="4b1fa-130">SignalR automatically chooses the best transport method that is within the capabilities of the server and client.</span></span>

## <a name="hubs"></a><span data-ttu-id="4b1fa-131">허브</span><span class="sxs-lookup"><span data-stu-id="4b1fa-131">Hubs</span></span>

<span data-ttu-id="4b1fa-132">SignalR은 *허브*를 통해서 클라이언트와 서버 간의 통신을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="4b1fa-132">SignalR uses *hubs* to communicate between clients and servers.</span></span>

<span data-ttu-id="4b1fa-133">허브는 클라이언트와 서버가 서로 상대방의 메서드를 호출할 수 있게 해주는 높은 수준의 파이프라인입니다.</span><span class="sxs-lookup"><span data-stu-id="4b1fa-133">A hub is a high-level pipeline that allows a client and server to call methods on each other.</span></span> <span data-ttu-id="4b1fa-134">SignalR은 클라이언트에서 서버의 메서드를 호출하거나 그 반대로 호출이 가능하도록, 컴퓨터의 경계를 넘어서는 디스패치 처리를 자동으로 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="4b1fa-134">SignalR handles the dispatching across machine boundaries automatically, allowing clients to call methods on the server and vice versa.</span></span> <span data-ttu-id="4b1fa-135">메서드에 강력한 형식의 매개 변수를 전달할 수 있으므로 모델 바인딩을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4b1fa-135">You can pass strongly-typed parameters to methods, which enables model binding.</span></span> <span data-ttu-id="4b1fa-136">SignalR은 JSON 기반의 텍스트 프로토콜 및 [MessagePack](https://msgpack.org/) 기반의 바이너리 프로토콜의 두 가지 기본 제공 허브 프로토콜을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="4b1fa-136">SignalR provides two built-in hub protocols: a text protocol based on JSON and a binary protocol based on [MessagePack](https://msgpack.org/).</span></span>  <span data-ttu-id="4b1fa-137">일반적으로 MessagePack이 JSON에 비해 작은 크기의 메시지를 만들어냅니다.</span><span class="sxs-lookup"><span data-stu-id="4b1fa-137">MessagePack generally creates smaller messages compared to JSON.</span></span> <span data-ttu-id="4b1fa-138">구형 브라우저는 MessagePack 프로토콜 기능을 제공하기 위해서 [XHR 수준 2](https://caniuse.com/#feat=xhr2)를 지원해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="4b1fa-138">Older browsers must support [XHR level 2](https://caniuse.com/#feat=xhr2) to provide MessagePack protocol support.</span></span>

<span data-ttu-id="4b1fa-139">허브는 클라이언트 쪽 메서드의 이름 및 매개 변수를 담고 있는 메시지를 전송해서 클라이언트 쪽 코드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="4b1fa-139">Hubs call client-side code by sending messages that contain the name and parameters of the client-side method.</span></span> <span data-ttu-id="4b1fa-140">메서드의 매개 변수로 전송된 개체는 구성된 프로토콜을 이용해서 역직렬화됩니다.</span><span class="sxs-lookup"><span data-stu-id="4b1fa-140">Objects sent as method parameters are deserialized using the configured protocol.</span></span> <span data-ttu-id="4b1fa-141">클라이언트는 클라이언트 쪽 코드에서 이름이 일치하는 메서드를 찾으려고 시도합니다.</span><span class="sxs-lookup"><span data-stu-id="4b1fa-141">The client tries to match the name to a method in the client-side code.</span></span> <span data-ttu-id="4b1fa-142">클라이언트가 일치하는 이름을 찾으면 역직렬화된 매개 변수 데이터를 전달하여 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="4b1fa-142">When the client finds a match, it calls the method and passes to it the deserialized parameter data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="4b1fa-143">추가 자료</span><span class="sxs-lookup"><span data-stu-id="4b1fa-143">Additional resources</span></span>

* [<span data-ttu-id="4b1fa-144">ASP.NET Core SignalR 시작하기</span><span class="sxs-lookup"><span data-stu-id="4b1fa-144">Get started with SignalR for ASP.NET Core</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="4b1fa-145">지원되는 플랫폼</span><span class="sxs-lookup"><span data-stu-id="4b1fa-145">Supported Platforms</span></span>](xref:signalr/supported-platforms)
* [<span data-ttu-id="4b1fa-146">허브</span><span class="sxs-lookup"><span data-stu-id="4b1fa-146">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="4b1fa-147">JavaScript 클라이언트</span><span class="sxs-lookup"><span data-stu-id="4b1fa-147">JavaScript client</span></span>](xref:signalr/javascript-client)
