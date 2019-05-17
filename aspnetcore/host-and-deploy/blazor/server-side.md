---
title: Blazor 서버 쪽 호스트 및 배포
author: guardrex
description: ASP.NET Core를 사용하여 Blazor 서버 쪽 앱을 호스팅하고 배포하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 04/26/2019
uid: host-and-deploy/blazor/server-side
ms.openlocfilehash: 8e44be09a4cceba2509f3e86abf3ce5fd2d077bd
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64887768"
---
# <a name="host-and-deploy-blazor-server-side"></a><span data-ttu-id="8ef5e-103">Blazor 서버 쪽 호스트 및 배포</span><span class="sxs-lookup"><span data-stu-id="8ef5e-103">Host and deploy Blazor server-side</span></span>

<span data-ttu-id="8ef5e-104">작성자: [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com) 및 [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="8ef5e-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), and [Daniel Roth](https://github.com/danroth27)</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="8ef5e-105">호스트 구성 값</span><span class="sxs-lookup"><span data-stu-id="8ef5e-105">Host configuration values</span></span>

<span data-ttu-id="8ef5e-106">[서버 쪽 호스팅 모델](xref:blazor/hosting-models#server-side)을 사용하는 서버 쪽 앱은 [제네릭 호스트 구성 값](xref:fundamentals/host/generic-host#host-configuration)을 허용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8ef5e-106">Server-side apps that use the [server-side hosting model](xref:blazor/hosting-models#server-side) can accept [Generic Host configuration values](xref:fundamentals/host/generic-host#host-configuration).</span></span>

## <a name="deployment"></a><span data-ttu-id="8ef5e-107">배포</span><span class="sxs-lookup"><span data-stu-id="8ef5e-107">Deployment</span></span>

<span data-ttu-id="8ef5e-108">[서버 쪽 호스팅 모델](xref:blazor/hosting-models#server-side)을 사용하면 Blazor가 서버의 ASP.NET Core 앱 내에서 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ef5e-108">With the [server-side hosting model](xref:blazor/hosting-models#server-side), Blazor is executed on the server from within an ASP.NET Core app.</span></span> <span data-ttu-id="8ef5e-109">UI 업데이트, 이벤트 처리 및 JavaScript 호출은 [SignalR](xref:signalr/introduction) 연결을 통해 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ef5e-109">UI updates, event handling, and JavaScript calls are handled over a [SignalR](xref:signalr/introduction) connection.</span></span>

<span data-ttu-id="8ef5e-110">ASP.NET Core 앱을 호스팅할 수 있는 웹 서버가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="8ef5e-110">A web server capable of hosting an ASP.NET Core app is required.</span></span> <span data-ttu-id="8ef5e-111">Visual Studio에는 **Blazor(서버 쪽)** 프로젝트 템플릿([dotnet new](/dotnet/core/tools/dotnet-new) 명령을 사용하는 경우 `blazorserverside` 템플릿)이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ef5e-111">Visual Studio includes the **Blazor (server-side)** project template (`blazorserverside` template when using the [dotnet new](/dotnet/core/tools/dotnet-new) command).</span></span>

<!--

**INSERT: Concerns are the same as publishing an ASP.NET Core SignalR app**

**INSERT: Content on the Azure SignalR Service**

**INSERT: Manually turn on WebSockets support**

-->

## <a name="additional-resources"></a><span data-ttu-id="8ef5e-112">추가 자료</span><span class="sxs-lookup"><span data-stu-id="8ef5e-112">Additional resources</span></span>

* <xref:signalr/introduction>
* <xref:host-and-deploy/index>
* <xref:tutorials/publish-to-azure-webapp-using-vs>
* [<span data-ttu-id="8ef5e-113">Azure App Service에 ASP.NET Core 미리 보기 릴리스 배포</span><span class="sxs-lookup"><span data-stu-id="8ef5e-113">Deploy ASP.NET Core preview release to Azure App Service</span></span>](xref:host-and-deploy/azure-apps/index#deploy-aspnet-core-preview-release-to-azure-app-service)
