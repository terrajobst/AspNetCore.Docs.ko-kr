---
title: 지원 되는 플랫폼 ASP.NET Core SignalR
author: bradygaster
description: ASP.NET Core SignalR에 대해 지원 되는 플랫폼에 대해 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 01/16/2020
no-loc:
- SignalR
uid: signalr/supported-platforms
ms.openlocfilehash: 054965921c87c1a9be27e5ddaa8a87b0fa1f4113
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78655149"
---
# <a name="aspnet-core-signalr-supported-platforms"></a><span data-ttu-id="cc43c-103">ASP.NET Core SignalR 지원 플랫폼</span><span class="sxs-lookup"><span data-stu-id="cc43c-103">ASP.NET Core SignalR supported platforms</span></span>

## <a name="server-system-requirements"></a><span data-ttu-id="cc43c-104">서버 시스템 요구 사항</span><span class="sxs-lookup"><span data-stu-id="cc43c-104">Server system requirements</span></span>

<span data-ttu-id="cc43c-105">ASP.NET Core에 대 한 SignalR는 ASP.NET Core에서 지원 되는 모든 서버 플랫폼을 지원 합니다.</span><span class="sxs-lookup"><span data-stu-id="cc43c-105">SignalR for ASP.NET Core supports any server platform that ASP.NET Core supports.</span></span>

## <a name="javascript-client"></a><span data-ttu-id="cc43c-106">JavaScript 클라이언트</span><span class="sxs-lookup"><span data-stu-id="cc43c-106">JavaScript client</span></span>

<span data-ttu-id="cc43c-107">[JavaScript 클라이언트](xref:signalr/javascript-client) 는 nodejs 8 이상 버전과 다음 브라우저에서 실행 됩니다.</span><span class="sxs-lookup"><span data-stu-id="cc43c-107">The [JavaScript client](xref:signalr/javascript-client) runs on NodeJS 8 and later versions and the following browsers:</span></span>

| <span data-ttu-id="cc43c-108">브라우저</span><span class="sxs-lookup"><span data-stu-id="cc43c-108">Browser</span></span>                         | <span data-ttu-id="cc43c-109">버전</span><span class="sxs-lookup"><span data-stu-id="cc43c-109">Version</span></span>         |
| ------------------------------- | --------------- |
| <span data-ttu-id="cc43c-110">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="cc43c-110">Microsoft Edge</span></span>                  | <span data-ttu-id="cc43c-111">현재&dagger;</span><span class="sxs-lookup"><span data-stu-id="cc43c-111">Current&dagger;</span></span> |
| <span data-ttu-id="cc43c-112">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="cc43c-112">Mozilla Firefox</span></span>                 | <span data-ttu-id="cc43c-113">현재&dagger;</span><span class="sxs-lookup"><span data-stu-id="cc43c-113">Current&dagger;</span></span> |
| <span data-ttu-id="cc43c-114">Google Chrome(Android 포함)</span><span class="sxs-lookup"><span data-stu-id="cc43c-114">Google Chrome; includes Android</span></span> | <span data-ttu-id="cc43c-115">현재&dagger;</span><span class="sxs-lookup"><span data-stu-id="cc43c-115">Current&dagger;</span></span> |
| <span data-ttu-id="cc43c-116">Safari(iOS 포함)</span><span class="sxs-lookup"><span data-stu-id="cc43c-116">Safari; includes iOS</span></span>            | <span data-ttu-id="cc43c-117">현재&dagger;</span><span class="sxs-lookup"><span data-stu-id="cc43c-117">Current&dagger;</span></span> |
| <span data-ttu-id="cc43c-118">Microsoft Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="cc43c-118">Microsoft Internet Explorer</span></span>     | <span data-ttu-id="cc43c-119">11</span><span class="sxs-lookup"><span data-stu-id="cc43c-119">11</span></span>              |

<span data-ttu-id="cc43c-120">*현재* &dagger;는 브라우저의 최신 버전을 참조 합니다.</span><span class="sxs-lookup"><span data-stu-id="cc43c-120">&dagger;*Current* refers to the latest version of the browser.</span></span>

## <a name="net-client"></a><span data-ttu-id="cc43c-121">.NET 클라이언트</span><span class="sxs-lookup"><span data-stu-id="cc43c-121">.NET client</span></span>

<span data-ttu-id="cc43c-122">[.Net 클라이언트](xref:signalr/dotnet-client) 는 ASP.NET Core에서 지 원하는 모든 플랫폼에서 실행 됩니다.</span><span class="sxs-lookup"><span data-stu-id="cc43c-122">The [.NET client](xref:signalr/dotnet-client) runs on any platform supported by ASP.NET Core.</span></span> <span data-ttu-id="cc43c-123">예를 들어 xamarin 개발자는 xamarin.ios 11.14.0.4 이상을 사용 하 여 8.4.0.1 이상 및 iOS 앱을 사용 하 여 Android 앱을 빌드하는 데 [SignalR를 사용할 수 있습니다](https://github.com/aspnet/Announcements/issues/305) .</span><span class="sxs-lookup"><span data-stu-id="cc43c-123">For example, [Xamarin developers can use SignalR](https://github.com/aspnet/Announcements/issues/305) for building Android apps using Xamarin.Android 8.4.0.1 and later and iOS apps using Xamarin.iOS 11.14.0.4 and later.</span></span>

<span data-ttu-id="cc43c-124">서버에서 IIS를 실행 하는 경우 Websocket 전송에는 Windows Server 2012 이상에서 IIS 8.0 이상이 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="cc43c-124">If the server runs IIS, the WebSockets transport requires IIS 8.0 or later on Windows Server 2012 or later.</span></span> <span data-ttu-id="cc43c-125">다른 전송 방식들은 모든 플랫폼에서 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="cc43c-125">Other transports are supported on all platforms.</span></span>

## <a name="java-client"></a><span data-ttu-id="cc43c-126">Java 클라이언트</span><span class="sxs-lookup"><span data-stu-id="cc43c-126">Java client</span></span>

<span data-ttu-id="cc43c-127">[Java 클라이언트](xref:signalr/java-client) 는 java 8 이상 버전을 지원 합니다.</span><span class="sxs-lookup"><span data-stu-id="cc43c-127">The [Java client](xref:signalr/java-client) supports Java 8 and later versions.</span></span>

## <a name="unsupported-clients"></a><span data-ttu-id="cc43c-128">지원되지 않는 클라이언트</span><span class="sxs-lookup"><span data-stu-id="cc43c-128">Unsupported clients</span></span>

<span data-ttu-id="cc43c-129">다음 클라이언트는 사용할 수는 있지만 실험적이거나 비공식적입니다.</span><span class="sxs-lookup"><span data-stu-id="cc43c-129">The following clients are available but are experimental or unofficial.</span></span> <span data-ttu-id="cc43c-130">현재 지원 되지 않으며 그렇지 않을 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cc43c-130">They aren't currently supported and may never be.</span></span>

* <span data-ttu-id="cc43c-131">[C++클라이언트로](https://github.com/aspnet/SignalR-Client-Cpp)</span><span class="sxs-lookup"><span data-stu-id="cc43c-131">[C++ client](https://github.com/aspnet/SignalR-Client-Cpp)</span></span>

* <span data-ttu-id="cc43c-132">[Swift 클라이언트](https://github.com/moozzyk/SignalR-Client-Swift)</span><span class="sxs-lookup"><span data-stu-id="cc43c-132">[Swift client](https://github.com/moozzyk/SignalR-Client-Swift)</span></span>
