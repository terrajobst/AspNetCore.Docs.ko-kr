---
title: ASP.NET Core SignalR 지원 플랫폼
author: bradygaster
description: ASP.NET Core SignalR에 대해 지원 되는 플랫폼에 대해 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/01/2019
uid: signalr/supported-platforms
ms.openlocfilehash: 1be7a307710e6e522c0088fd1ca01da11a13eda1
ms.sourcegitcommit: 77c8be22d5e88dd710f42c739748869f198865dd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/01/2019
ms.locfileid: "73426970"
---
# <a name="aspnet-core-signalr-supported-platforms"></a><span data-ttu-id="4650a-103">ASP.NET Core SignalR 지원 플랫폼</span><span class="sxs-lookup"><span data-stu-id="4650a-103">ASP.NET Core SignalR supported platforms</span></span>

## <a name="server-system-requirements"></a><span data-ttu-id="4650a-104">서버 시스템 요구 사항</span><span class="sxs-lookup"><span data-stu-id="4650a-104">Server system requirements</span></span>

<span data-ttu-id="4650a-105">ASP.NET Core에 대 한 SignalR는 ASP.NET Core에서 지원 되는 모든 서버 플랫폼을 지원 합니다.</span><span class="sxs-lookup"><span data-stu-id="4650a-105">SignalR for ASP.NET Core supports any server platform that ASP.NET Core supports.</span></span>

## <a name="javascript-client"></a><span data-ttu-id="4650a-106">JavaScript 클라이언트</span><span class="sxs-lookup"><span data-stu-id="4650a-106">JavaScript client</span></span>

<span data-ttu-id="4650a-107">[JavaScript 클라이언트](https://www.npmjs.com/package/@aspnet/signalr) 는 nodejs 8 이상 버전과 다음 브라우저에서 실행 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4650a-107">The [JavaScript client](https://www.npmjs.com/package/@aspnet/signalr) runs on NodeJS 8 and later versions and the following browsers:</span></span>

| <span data-ttu-id="4650a-108">브라우저</span><span class="sxs-lookup"><span data-stu-id="4650a-108">Browser</span></span>                         | <span data-ttu-id="4650a-109">Version</span><span class="sxs-lookup"><span data-stu-id="4650a-109">Version</span></span>         |
| ------------------------------- | --------------- |
| <span data-ttu-id="4650a-110">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="4650a-110">Microsoft Edge</span></span>                  | <span data-ttu-id="4650a-111">현재&dagger;</span><span class="sxs-lookup"><span data-stu-id="4650a-111">Current&dagger;</span></span> |
| <span data-ttu-id="4650a-112">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="4650a-112">Mozilla Firefox</span></span>                 | <span data-ttu-id="4650a-113">현재&dagger;</span><span class="sxs-lookup"><span data-stu-id="4650a-113">Current&dagger;</span></span> |
| <span data-ttu-id="4650a-114">Google Chrome; Android 포함</span><span class="sxs-lookup"><span data-stu-id="4650a-114">Google Chrome; includes Android</span></span> | <span data-ttu-id="4650a-115">현재&dagger;</span><span class="sxs-lookup"><span data-stu-id="4650a-115">Current&dagger;</span></span> |
| <span data-ttu-id="4650a-116">Safari iOS 포함</span><span class="sxs-lookup"><span data-stu-id="4650a-116">Safari; includes iOS</span></span>            | <span data-ttu-id="4650a-117">현재&dagger;</span><span class="sxs-lookup"><span data-stu-id="4650a-117">Current&dagger;</span></span> |
| <span data-ttu-id="4650a-118">Microsoft Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="4650a-118">Microsoft Internet Explorer</span></span>     | <span data-ttu-id="4650a-119">11</span><span class="sxs-lookup"><span data-stu-id="4650a-119">11</span></span>              |

<span data-ttu-id="4650a-120">*현재* &dagger;는 브라우저의 최신 버전을 참조 합니다.</span><span class="sxs-lookup"><span data-stu-id="4650a-120">&dagger;*Current* refers to the latest version of the browser.</span></span>

## <a name="net-client"></a><span data-ttu-id="4650a-121">.NET 클라이언트</span><span class="sxs-lookup"><span data-stu-id="4650a-121">.NET client</span></span>

<span data-ttu-id="4650a-122">[.Net 클라이언트](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR/) 는 ASP.NET Core에서 지 원하는 모든 플랫폼에서 실행 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4650a-122">The [.NET client](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR/) runs on any platform supported by ASP.NET Core.</span></span> <span data-ttu-id="4650a-123">예를 들어 xamarin 개발자는 xamarin.ios 11.14.0.4 이상을 사용 하 여 Xamarin.ios 8.4.0.1 이상 및 iOS 앱을 사용 하 여 Android 앱을 빌드하는 데 [SignalR를 사용할 수 있습니다](https://github.com/aspnet/Announcements/issues/305) .</span><span class="sxs-lookup"><span data-stu-id="4650a-123">For example, [Xamarin developers can use SignalR](https://github.com/aspnet/Announcements/issues/305) for building Android apps using Xamarin.Android 8.4.0.1 and later and iOS apps using Xamarin.iOS 11.14.0.4 and later.</span></span>

<span data-ttu-id="4650a-124">서버에서 IIS를 실행 하는 경우 Websocket 전송에는 Windows Server 2012 이상에서 IIS 8.0 이상이 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="4650a-124">If the server runs IIS, the WebSockets transport requires IIS 8.0 or later on Windows Server 2012 or later.</span></span> <span data-ttu-id="4650a-125">다른 전송은 모든 플랫폼에서 지원 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4650a-125">Other transports are supported on all platforms.</span></span>

## <a name="java-client"></a><span data-ttu-id="4650a-126">Java 클라이언트</span><span class="sxs-lookup"><span data-stu-id="4650a-126">Java client</span></span>

<span data-ttu-id="4650a-127">[Java 클라이언트](https://search.maven.org/artifact/com.microsoft.aspnet/signalr) 는 java 8 이상 버전을 지원 합니다.</span><span class="sxs-lookup"><span data-stu-id="4650a-127">The [Java client](https://search.maven.org/artifact/com.microsoft.aspnet/signalr) supports Java 8 and later versions.</span></span>

## <a name="unsupported-clients"></a><span data-ttu-id="4650a-128">지원 되지 않는 클라이언트</span><span class="sxs-lookup"><span data-stu-id="4650a-128">Unsupported clients</span></span>

<span data-ttu-id="4650a-129">다음 클라이언트는 사용할 수 있지만 실험적 이거나 비공식적입니다.</span><span class="sxs-lookup"><span data-stu-id="4650a-129">The following clients are available but are experimental or unofficial.</span></span> <span data-ttu-id="4650a-130">현재 지원 되지 않으며 그렇지 않을 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4650a-130">They aren't currently supported and may never be.</span></span>

* [<span data-ttu-id="4650a-131">C++클라이언트로</span><span class="sxs-lookup"><span data-stu-id="4650a-131">C++ client</span></span>](https://github.com/aspnet/SignalR/tree/master/clients/cpp)

* [<span data-ttu-id="4650a-132">Swift 클라이언트</span><span class="sxs-lookup"><span data-stu-id="4650a-132">Swift client</span></span>](https://github.com/moozzyk/SignalR-Client-Swift)
