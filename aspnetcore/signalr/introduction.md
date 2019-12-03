---
title: ASP.NET Core SignalR 소개
author: bradygaster
description: ASP.NET Core SignalR 라이브러리에서 실시간 기능을 앱에 추가 하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/27/2019
no-loc:
- SignalR
uid: signalr/introduction
ms.openlocfilehash: e84dd0d086cbfc80a80bc10baa33979da9b5d137
ms.sourcegitcommit: 3b6b0a54b20dc99b0c8c5978400c60adf431072f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74717236"
---
# <a name="introduction-to-aspnet-core-opno-locsignalr"></a><span data-ttu-id="96722-103">ASP.NET Core SignalR 소개</span><span class="sxs-lookup"><span data-stu-id="96722-103">Introduction to ASP.NET Core SignalR</span></span>

## <a name="what-is-opno-locsignalr"></a><span data-ttu-id="96722-104">SignalR이란?</span><span class="sxs-lookup"><span data-stu-id="96722-104">What is SignalR?</span></span>

<span data-ttu-id="96722-105">ASP.NET Core SignalR는 응용 프로그램에 실시간 웹 기능 추가를 간소화 하는 오픈 소스 라이브러리입니다.</span><span class="sxs-lookup"><span data-stu-id="96722-105">ASP.NET Core SignalR is an open-source library that simplifies adding real-time web functionality to apps.</span></span> <span data-ttu-id="96722-106">실시간 웹 기능을 사용 하면 서버측 코드에서 클라이언트에 콘텐츠를 즉시 푸시할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="96722-106">Real-time web functionality enables server-side code to push content to clients instantly.</span></span>

<span data-ttu-id="96722-107">SignalR에 적합 한 후보:</span><span class="sxs-lookup"><span data-stu-id="96722-107">Good candidates for SignalR:</span></span>

* <span data-ttu-id="96722-108">서버에서 빈도가 높은 업데이트가 필요한 앱입니다.</span><span class="sxs-lookup"><span data-stu-id="96722-108">Apps that require high frequency updates from the server.</span></span> <span data-ttu-id="96722-109">예를 들면 게임, 소셜 네트워크, 투표, 경매, 지도 및 GPS 앱입니다.</span><span class="sxs-lookup"><span data-stu-id="96722-109">Examples are gaming, social networks, voting, auction, maps, and GPS apps.</span></span>
* <span data-ttu-id="96722-110">대시보드 및 모니터링 앱.</span><span class="sxs-lookup"><span data-stu-id="96722-110">Dashboards and monitoring apps.</span></span> <span data-ttu-id="96722-111">예를 들면 회사 대시보드, 인스턴트 판매 업데이트 또는 여행 경고가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="96722-111">Examples include company dashboards, instant sales updates, or travel alerts.</span></span>
* <span data-ttu-id="96722-112">공동 작업 앱.</span><span class="sxs-lookup"><span data-stu-id="96722-112">Collaborative apps.</span></span> <span data-ttu-id="96722-113">화이트 보드 앱 및 팀 모임 소프트웨어는 공동 작업 앱의 예입니다.</span><span class="sxs-lookup"><span data-stu-id="96722-113">Whiteboard apps and team meeting software are examples of collaborative apps.</span></span>
* <span data-ttu-id="96722-114">알림이 필요한 앱입니다.</span><span class="sxs-lookup"><span data-stu-id="96722-114">Apps that require notifications.</span></span> <span data-ttu-id="96722-115">소셜 네트워크, 메일, 채팅, 게임, 여행 경고 및 기타 많은 앱은 알림을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="96722-115">Social networks, email, chat, games, travel alerts, and many other apps use notifications.</span></span>

SignalR<span data-ttu-id="96722-116">는 서버-클라이언트 [원격 프로시저 호출 (RPC)](https://wikipedia.org/wiki/Remote_procedure_call)을 만들기 위한 API를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="96722-116"> provides an API for creating server-to-client [remote procedure calls (RPC)](https://wikipedia.org/wiki/Remote_procedure_call).</span></span> <span data-ttu-id="96722-117">Rpc는 서버 쪽 .NET Core 코드에서 클라이언트에 대 한 JavaScript 함수를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="96722-117">The RPCs call JavaScript functions on clients from server-side .NET Core code.</span></span>

<span data-ttu-id="96722-118">ASP.NET Core에 대 한 SignalR 몇 가지 기능은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="96722-118">Here are some features of SignalR for ASP.NET Core:</span></span>

* <span data-ttu-id="96722-119">연결 관리를 자동으로 처리 합니다.</span><span class="sxs-lookup"><span data-stu-id="96722-119">Handles connection management automatically.</span></span>
* <span data-ttu-id="96722-120">모든 연결 된 클라이언트에 동시에 메시지를 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="96722-120">Sends messages to all connected clients simultaneously.</span></span> <span data-ttu-id="96722-121">예를 들어 대화방입니다.</span><span class="sxs-lookup"><span data-stu-id="96722-121">For example, a chat room.</span></span>
* <span data-ttu-id="96722-122">특정 클라이언트나 클라이언트 그룹으로 메시지를 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="96722-122">Sends messages to specific clients or groups of clients.</span></span>
* <span data-ttu-id="96722-123">늘어난 트래픽을 처리 하도록 크기를 조정 합니다.</span><span class="sxs-lookup"><span data-stu-id="96722-123">Scales to handle increasing traffic.</span></span>

<span data-ttu-id="96722-124">소스는 [GitHub의SignalR 리포지토리에서](https://github.com/aspnet/AspNetCore/tree/master/src/SignalR)호스팅됩니다.</span><span class="sxs-lookup"><span data-stu-id="96722-124">The source is hosted in a [SignalR repository on GitHub](https://github.com/aspnet/AspNetCore/tree/master/src/SignalR).</span></span>

## <a name="transports"></a><span data-ttu-id="96722-125">전송</span><span class="sxs-lookup"><span data-stu-id="96722-125">Transports</span></span>

SignalR<span data-ttu-id="96722-126">는 실시간 통신을 처리 하는 다음과 같은 기술을 지원 합니다 (정상적인 대체 순서 대로).</span><span class="sxs-lookup"><span data-stu-id="96722-126"> supports the following techniques for handling real-time communication (in order of graceful fallback):</span></span>

* [<span data-ttu-id="96722-127">WebSockets</span><span class="sxs-lookup"><span data-stu-id="96722-127">WebSockets</span></span>](https://tools.ietf.org/html/rfc7118)
* <span data-ttu-id="96722-128">서버에서 보낸 이벤트</span><span class="sxs-lookup"><span data-stu-id="96722-128">Server-Sent Events</span></span>
* <span data-ttu-id="96722-129">긴 폴링</span><span class="sxs-lookup"><span data-stu-id="96722-129">Long Polling</span></span>

SignalR<span data-ttu-id="96722-130">는 서버 및 클라이언트의 기능 내에서 가장 적합 한 전송 방법을 자동으로 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="96722-130"> automatically chooses the best transport method that is within the capabilities of the server and client.</span></span>

## <a name="hubs"></a><span data-ttu-id="96722-131">허브</span><span class="sxs-lookup"><span data-stu-id="96722-131">Hubs</span></span>

SignalR<span data-ttu-id="96722-132">는 *허브* 를 사용 하 여 클라이언트와 서버 간에 통신 합니다.</span><span class="sxs-lookup"><span data-stu-id="96722-132"> uses *hubs* to communicate between clients and servers.</span></span>

<span data-ttu-id="96722-133">허브는 클라이언트와 서버가 서로 메서드를 호출할 수 있도록 하는 고급 파이프라인입니다.</span><span class="sxs-lookup"><span data-stu-id="96722-133">A hub is a high-level pipeline that allows a client and server to call methods on each other.</span></span> SignalR<span data-ttu-id="96722-134">은 컴퓨터 경계에서 디스패치를 자동으로 처리 하 여 클라이언트가 서버에서 메서드를 호출할 수 있도록 하 고 그 반대의 경우도 마찬가지입니다.</span><span class="sxs-lookup"><span data-stu-id="96722-134"> handles the dispatching across machine boundaries automatically, allowing clients to call methods on the server and vice versa.</span></span> <span data-ttu-id="96722-135">강력한 형식의 매개 변수를 메서드에 전달 하 여 모델 바인딩을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="96722-135">You can pass strongly-typed parameters to methods, which enables model binding.</span></span> SignalR<span data-ttu-id="96722-136">는 JSON 기반의 텍스트 프로토콜과 [MessagePack](https://msgpack.org/)기반 이진 프로토콜을 제공 하는 두 가지 기본 제공 허브 프로토콜을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="96722-136"> provides two built-in hub protocols: a text protocol based on JSON and a binary protocol based on [MessagePack](https://msgpack.org/).</span></span>  <span data-ttu-id="96722-137">MessagePack는 일반적으로 JSON과 비교 하 여 더 작은 메시지를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="96722-137">MessagePack generally creates smaller messages compared to JSON.</span></span> <span data-ttu-id="96722-138">이전 브라우저는 MessagePack 프로토콜 지원을 제공 하기 위해 [Xhr 수준 2](https://caniuse.com/#feat=xhr2) 를 지원 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="96722-138">Older browsers must support [XHR level 2](https://caniuse.com/#feat=xhr2) to provide MessagePack protocol support.</span></span>

<span data-ttu-id="96722-139">허브는 클라이언트 쪽 메서드의 이름 및 매개 변수를 포함 하는 메시지를 전송 하 여 클라이언트 쪽 코드를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="96722-139">Hubs call client-side code by sending messages that contain the name and parameters of the client-side method.</span></span> <span data-ttu-id="96722-140">메서드 매개 변수로 보낸 개체는 구성 된 프로토콜을 사용 하 여 deserialize 됩니다.</span><span class="sxs-lookup"><span data-stu-id="96722-140">Objects sent as method parameters are deserialized using the configured protocol.</span></span> <span data-ttu-id="96722-141">클라이언트는 클라이언트 쪽 코드의 메서드와 이름을 일치 시 키 려 고 시도 합니다.</span><span class="sxs-lookup"><span data-stu-id="96722-141">The client tries to match the name to a method in the client-side code.</span></span> <span data-ttu-id="96722-142">클라이언트에서 일치 하는 항목을 찾으면 메서드를 호출 하 여 deserialize 된 매개 변수 데이터를 전달 합니다.</span><span class="sxs-lookup"><span data-stu-id="96722-142">When the client finds a match, it calls the method and passes to it the deserialized parameter data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="96722-143">추가 자료</span><span class="sxs-lookup"><span data-stu-id="96722-143">Additional resources</span></span>

* <span data-ttu-id="96722-144">[ASP.NET Core에 대 한 SignalR 시작](xref:tutorials/signalr)</span><span class="sxs-lookup"><span data-stu-id="96722-144">[Get started with SignalR for ASP.NET Core](xref:tutorials/signalr)</span></span>
* [<span data-ttu-id="96722-145">지원되는 플랫폼</span><span class="sxs-lookup"><span data-stu-id="96722-145">Supported Platforms</span></span>](xref:signalr/supported-platforms)
* [<span data-ttu-id="96722-146">허브</span><span class="sxs-lookup"><span data-stu-id="96722-146">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="96722-147">JavaScript 클라이언트</span><span class="sxs-lookup"><span data-stu-id="96722-147">JavaScript client</span></span>](xref:signalr/javascript-client)
